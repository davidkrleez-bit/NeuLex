# 04 · Encounter-based review model

[← Back to plan index](../neulang-plan.md)

**David decided:**
- Spacing close to Anki's, but driven by how many times the user **encounters** a word.
- Enough encounters move a word to a **deep** state.
- A deep word that isn't encountered for **about 2 months** comes back as a flashcard or short exercise.
- **Every encounter is tracked per user with timestamps.**
- **Same-day repeat encounters count for less.**

**David decided (planning brief, 2026-10-13):**
- **Hard constraint:** the AI may generate and may flag. It must **never be the sole authority that promotes** an interval. Uncertainty resolves to **no promotion**. A bare "correct!" must never lengthen a card.
- A successful production counts as a spaced-repetition session for that word.
- **Rejected:** any loop that requires a production per word to "prove" acquisition, with the AI demoting words. The cheap demotion signal is **re-tapping a word previously marked known**.
- Function words are never study items.

What follows is the algorithm that implements this (**Recommendation — needs David's check** unless noted).

---

## 4.0 Promotion authority (the rule every other part obeys)

A **promotion** is anything that lengthens a word's next review or moves it to a later state:
- an FSRS rating of Hard / Good / Easy;
- graduating Learning → Reviewing;
- Reviewing → Deep;
- Deep → Mastered;
- triage "already know" → Known.

**Allowed authorities for a promotion:**

| Authority | Example | Can promote? |
|---|---|---|
| **Answer key** | Cloze with one keyed answer (inflection table), MCQ, speed-round card, placement MCQ | ✅ |
| **Learner** | Learner self-rates a recall or production ("How did that feel?") — the same authority Anki uses | ✅ |
| **Deterministic checks** | Lemma present, correct inflection from the lexicon, part-of-speech tag plausible, not copied from the bot's message, no lookup of that word in the last 10 min | Necessary, never sufficient |
| **AI judge** | LLM rubric verdict on a free-text sentence | ❌ **Never.** It may only **veto** (flag → no promotion) or explain |

**Rules.**
1. **Free-text production promotes only if all of the following hold:**
   - the deterministic checks pass;
   - the AI raised **no flag** and its confidence is ≥ 0.8 (otherwise, uncertainty → no promotion);
   - the learner confirms with a self-rating on the post-chat recap card (§4.2b).
2. **The maximum rating from a production is Good** (never Easy).
3. **Every production-based promotion is provisional.** The word's next review is always an **answer-keyed** item (cloze or recall card). If that fails, the provisional gain is reversed (rating rewritten as Again in the replay). So a false accept by the AI and the learner survives at most one interval.
4. **The AI never demotes.** Demotion comes only from:
   - keyed failures;
   - learner self-ratings of Again;
   - **re-tapping a word previously marked known** (David's signal).
5. **Feedback UI never shows a bare "Correct!".** Every verdict shows the learner's sentence next to the reference definition and an example, so the learner's own rating is informed.

## 4.0b Answers to the two pre-scheduler questions (brief §6)

### D4.6 Does an incorrect production count as an SRS session? — **Recommendation — needs David's check**

**Options.**
- **A. Yes, as a lapse (Again).** The AI would then be demoting, which conflicts with the "AI never demotes" rule and with David's rejection of AI-driven demotion. An AI false reject would also corrupt the stored state.
- **B. Yes, as Hard.** Same problem, smaller.
- **C. No** (recommended).

**Recommendation: C.** An incorrect, flagged or uncertain production is:
- **logged** as an `encounter_event` with `outcome = incorrect | flagged | uncertain` (David decided that every encounter is tracked);
- given **0 encounter points** and no FSRS update;
- used to **queue a keyed cloze check** for that word in the next speed round. The keyed check is the SRS session.

Exception: if the **learner** self-rates "I didn't know how to use it" (Again), that's learner authority and counts as a lapse.

### D4.7 Textercise gate: "appeared" or "used correctly"? — **Recommendation — needs David's check**

**Options.**
- **A. "Appeared".** Weak: pasting a list of words would pass.
- **B. "Used correctly" (AI-judged).** Makes the AI the gatekeeper. It's not an interval promotion, but false rejects frustrate users and the gate becomes an AI-authority surface.
- **C. Split gate from scheduling** (recommended).

**Recommendation: C.**
- **Finishing the Textercise** requires each target to **appear in a learner-written sentence that passes the deterministic checks**:
  - the sentence has ≥ 4 words;
  - the target is used as the expected part of speech;
  - the target wasn't copied from the bot's turn;
  - the message isn't a bare word list.
- AI flags are shown, with a "try again" option. The learner may finish anyway, but **flagged words don't promote**.
- **Scheduling credit** follows §4.0 only. The gate is stronger than "appeared", and the AI is never the authority.
- The Textercise is **optional and labelled as AI chat** (see [05](05-lessons-and-exercises.md)), so the gate never blocks completing the lesson itself.

---

## 4.1 Algorithm choice: SM-2 vs FSRS vs custom

### D4.1 Memory model — **Recommendation — needs David's check**

| Option | What it is | Pros | Cons for NeuLang |
|---|---|---|---|
| **SM-2** (classic Anki) | Ease factor × interval after each graded review | Simple, well known | Only models graded reviews; known to be worse at predicting recall; no idea of "reading the word in a story" |
| **FSRS-6** (Anki's modern scheduler) | Difficulty / Stability / Retrievability model fitted on huge review logs | Better recall prediction than SM-2 on **99.6%** of 10k benchmark collections (log loss) [S1]; open source; a **Dart package exists** (`fsrs` on pub.dev) [S2]; handles same-day reviews explicitly | Also built for graded reviews; passive encounters aren't part of it |
| **Half-Life Regression** (Duolingo) | p = 2^(−Δ/h), with half-life h learned from features (e.g. exposure counts) [S3] | Designed for in-app practice with mixed signals; cut prediction error >45% and raised engagement 12% at Duolingo [S3] | Needs a lot of your own data to fit; no ready-made parameters |
| **Pure encounter counter** (David's sketch taken literally) | Word becomes deep after N encounters | Easy to explain | Ignores *when* encounters happened and whether recall succeeded; 10 reads in one evening ≠ known |

**Evidence on encounters.**
- Incidental learning from reading does work, and more repetitions help: across studies the correlation is r ≈ .34 [S4].
- About 10 encounters bring sizeable gains, but full knowledge needs more [S5]. A jump appears around 10–17 exposures [S6].
- **Retrieval beats exposure.** In Karpicke & Roediger (2008), repeated retrieval led to ~80% recall after a week vs ~33–36% when learners only restudied [S7]. A passive encounter is weaker evidence than a successful recall.
- **Production is stronger still.** Using a word in a sentence or composition raises learning beyond reading alone (sentence writing +11.5%, composition +21.0% in a meta-analysis) [S8].
- **Spacing matters.** The best gap is roughly 20–40% of the retention interval for short horizons and 5–10% for year-long horizons [S9]. A 2-month resurfacing after a word is well consolidated fits a long-term goal.

**Recommendation: a hybrid.**
1. **FSRS-6 (via the Dart `fsrs` package) is the memory model.** It is updated only by **graded retrievals whose authority is an answer key or the learner** (§4.0): recognition MCQ, keyed cloze/recall, speed-round card, and learner-confirmed production.
2. **Encounter points (E)** are the progress model. They are earned from *every* encounter, weighted by type and discounted for same-day repeats. E drives the state machine (Candidate → … → Deep → Mastered) and David's "enough encounters → deep" rule.
3. **Passive encounters nudge FSRS gently.** A word read in context gives a small, capped stability boost, so an often-read word isn't scheduled too aggressively. It never counts as a success on its own.
4. **Event-sourced.** All raw encounters are stored, so the model can be re-run later. Once there are a few hundred active users, fit an HLR-style model on NeuLang's own data, where passive counts become features with learned weights instead of guesses.

This keeps David's encounter-centric idea and makes the deep state *mean* something (it requires recall evidence, not just exposure). It also avoids inventing a scheduler from scratch.

## 4.2 Encounter types and weights

### D4.2 Initial weights — **Recommendation — needs David's check** (to be re-fitted from data)

| Kind | Example | Modality | E weight | Graded? → FSRS rating |
|---|---|---|---|---|
| `passive_read` | Word appears in a paragraph the user actually read | read | **0.5** | No (small capped boost) |
| `passive_listen` | Word heard in podcast/story audio (transcript off) | listen | **0.15** (listening-familiarity score; doesn't count toward Deep) | No |
| `passive_listen_read` | Heard with transcript on | both | 0.5 (counted as a read) | No |
| `lookup` | User tapped the word for its translation | read | **0.25** *and a lapse signal* | **Re-tap of a word marked known/deep = Again** (David's demotion signal; learner authority). Neutral for new words |
| `anchor` | Picked image / saw gloss at introduction | — | 0.5 | No (starts FSRS learning steps) |
| `recognition` | MCQ, matching, image pick (answer key) | — | **1.0** | Yes: wrong = Again; correct slow (> p75 latency) = Hard; correct = Good; fast correct = Easy |
| `recall` | Keyed cloze, speed-round card, type the word (answer key or learner self-rating) | — | **1.5** | Yes: same mapping; "hint used" caps at Hard |
| `production` | Target used in chat, opinion or picture description | write | **2.0** if promotion rules pass; **0** if flagged/uncertain/incorrect (still logged) | **Only via §4.0:** deterministic checks + no AI flag (confidence ≥ 0.8) + learner self-rating, max Good, provisional until the next keyed review. Incorrect/flagged → no FSRS update, keyed cloze queued (D4.6) |
| `companion_use` | Target used in free companion chat | write | 1.0 (same rules) | Same as production |

Mapping notes:
- FSRS has four ratings (again / hard / good / easy) [S2].
- The **latency** thresholds are per user, based on their own moving median. Fast readers aren't penalised.
- **Why listening is low:** David's brief reframes podcasts as **listening training, not a vocabulary channel**, and the evidence supports it.
  - Hatami (2017) had 139 EFL learners meet 16 target words: readers outperformed listeners on all five word-knowledge dimensions.
  - Word frequency in the text helped learning through reading but **not** through listening [S10].
  - Listening events still feed a separate per-word **listening familiarity** score. It's shown in the Vault and used to choose the audio level, not to schedule.
- **Function words** (closed-class: articles, pronouns, prepositions, conjunctions, auxiliaries; about 300 lemmas, flagged `is_function_word` in the lexicon) are **never items** (David decided). They don't generate encounter events, which also cuts event volume a lot. They count as known for coverage.

### 4.2b Post-production recap card (learner authority)
After a Textercise, opinion or picture step, one compact card lists each target the learner used:
- their sentence;
- the reference definition and example;
- any AI flag, with a short explanation.

The learner taps one of **"Nailed it" (Good) / "Not sure" (no promotion) / "Didn't know it" (Again)** for each word. A flagged word only offers the last two. This takes about 3 seconds per word, and it is the only route by which production promotes an interval.

## 4.3 Same-day decay

**David decided** that same-day repeats count for less. Proposed implementation:

- **Local day** = the user's local calendar day starting at **04:00** (Anki's convention), so late-night study counts for the right day.
- **Passive** encounters of item *i* on the same local day: the *k*-th one earns weight × **0.5^(k−1)**. So 0.5, 0.25, 0.125…, total ≤ 1.0 point a day from reading.
- **Graded** encounters after a *success* on the same day earn **0.3×** E. FSRS still records them, but FSRS-6 already shrinks same-day stability gains (its short-term formula S′ = S·e^{w17·(G−3+w18)}·S^{−w19} gives diminishing returns) [S1][S2].
- **Graded failures** always count fully toward lapse handling (no discount).
- **Distinct context bonus:** the first encounter of the day *in a new source text* gets ×1.25. Seeing a word in different contexts builds better knowledge than seeing it repeated in one text.
- **Dwell check for passive reads:** a `passive_read` counts only if the paragraph was on screen for a plausible reading time (≥ ~150 ms per word, capped). This stops fast scrolling from farming encounters.

## 4.4 State machine

```
Candidate ──triage "learn"──▶ New ──anchor──▶ Learning ──graduates──▶ Reviewing ──criteria──▶ Deep ──passes resurfacing──▶ Mastered
    │                                    ▲                  │ fail                    │ fail                    │ fail
    └─triage "already know" + check ─▶ Known(probable)       └──────── relearn ◀───────┴─────────────────────────┘
```

| State | Meaning | Scheduled by | Exit criteria |
|---|---|---|---|
| **Candidate** | Not known per placement/lookup; eligible to be taught | Lesson planner | Triage "learn" → New; "already know" + pass check → Known(probable) |
| **New** | Chosen for today | Today's lesson | Anchor shown → Learning |
| **Learning** | Short-term steps | FSRS learning steps (1 min, 10 min; then next day) [S2] | Passes the final step → Reviewing |
| **Reviewing** | Normal SRS | FSRS, desired retention **0.90** | Deep criteria below |
| **Deep** | Long-term; no routine reviews | Encounter watchdog | 60 days with no encounter → resurfacing check |
| **Mastered** | Passed at least one deep resurfacing | Watchdog with longer gap | Fail → Reviewing |
| **Known (probable)** | From placement; never taught | Passive signals | Lookup → Candidate; occasionally sampled in resurfacing |

### D4.3 "Deep" criteria — **Recommendation — needs David's check**
A word becomes **Deep** when **all** of the following are true:
1. E ≥ **12** encounter points. That is roughly 10–17 weighted exposures, in line with the 10–17 exposure findings [S5][S6].
2. ≥ **3 successful answer-keyed retrievals on 3 different days**. Retrieval evidence matters more than exposure [S7]. Productions don't count toward this, per §4.0.
3. ≥ **1 learner-confirmed, unflagged production**, *or* encounters in ≥ **2 different source texts**. The word must have been used or seen in more than one context. Production stays optional (see [05](05-lessons-and-exercises.md)), so learners who opt out reach Deep via the 2-texts route.
4. FSRS stability S ≥ **21 days**, meaning the model predicts ≥ 90% recall three weeks out.

### D4.4 Resurfacing deep words — **David decided** (about 2 months); details are a recommendation
- **Watchdog:** each day, any Deep word whose *last encounter of any kind* was ≥ **60 days** ago enters the resurfacing pool. Since any encounter resets the clock, words seen in reading never need flashcards, which is exactly David's intent.
- **Format:** rotate between a speed-round card and a keyed gap-fill in a 1-sentence context, both answer-keyed. An optional micro production prompt can follow but doesn't decide the outcome (§4.0). At most 3 resurfacing items per lesson.
- **Pass (keyed)** → **Mastered**; the next resurfacing gap doubles (60 → 120 → 240 days).
- **Fail** (or lookup tap in reading) → back to **Reviewing**, FSRS rating Again, and the item gets priority in matched reading.
- **Probable-known words from placement** are spot-checked the same way: ~1 a day, to keep the size estimate honest.

### D4.5 What "known" means in user-facing numbers — **Recommendation — needs David's check**
- "**Words you know**" = Σ P(known) over all items. P comes from FSRS retrievability for taught items, and from the placement or passive estimate for untaught ones.
- "**Words learned with NeuLang**" = the count in Reviewing + Deep + Mastered.
- "**Mastered**" = count in Mastered only.

Showing all three keeps the numbers honest: no "you learned 2,000 words" right after the placement test.

## 4.5 Priority of items in a lesson

Speed-round and exercise slots are filled in this order:
1. Learning-step items due now.
2. Overdue Reviewing items, lowest retrievability first.
3. Deep resurfacing items.
4. Today's new words (second exposure).
5. Words the user looked up today in reading. This turns lookups into retrieval quickly.

## 4.6 Matching reading to words

Reading recommendations and generated stories target words that are:
- **Learning / Reviewing with retrievability 0.80–0.92** (about to be forgotten), so a passive encounter is valuable;
- **Deep words with last encounter > 40 days**, so reading can "save" them before they need a card.

This is the bridge that makes "encounters instead of flashcards" work in practice.

## 4.7 Data model for encounters

**David decided:** every encounter is stored per user with timestamps. Schema (Postgres, mirrored to on-device SQLite):

```sql
-- Append-only, one row per encounter. Never updated.
encounter_event (
  id               uuid primary key,          -- client-generated (idempotent sync)
  user_id          uuid not null,
  item_id          int  not null,             -- lemma or chunk
  sense_id         int,                       -- disambiguated sense, nullable
  occurred_at      timestamptz not null,      -- device clock
  received_at      timestamptz not null,      -- server clock (clock-skew audits)
  local_day        date not null,             -- user's local day, 04:00 cutoff
  source_type      text not null,             -- lesson_story | podcast | reader | chat | opinion | picture | speed_round | companion | placement | resurfacing
  source_id        uuid,                      -- text / lesson / message id
  position         int,                       -- token offset in source (dedupe + context)
  modality         text not null,             -- read | listen | write | speak
  kind             text not null,             -- passive_read | passive_listen | lookup | anchor | recognition | recall | production
  outcome          text,                      -- correct | incorrect | partial | flagged | uncertain | null for passive
  authority        text,                      -- answer_key | learner | none   (who decided the outcome; §4.0)
  ai_verdict       text,                      -- AI judge output, advisory only; never decides promotion
  ai_confidence    real,
  ai_flagged       boolean default false,
  promotion_applied boolean default false,    -- did this event lengthen an interval / advance state?
  provisional      boolean default false,     -- production-based promotion awaiting the next keyed review
  fsrs_rating      smallint,                  -- 1..4 or null
  latency_ms       int,
  hint_used        boolean default false,
  surface_form     text,                      -- the inflected form seen ("decided")
  weight_raw       real not null,
  weight_applied   real not null,             -- after same-day decay & bonuses
  client_event_id  text not null,             -- dedupe key
  app_version      text not null,
  algo_version     text not null              -- which weighting rules produced weight_applied
);

-- Current state per user × item (derived; can be fully rebuilt from events).
item_state (
  user_id, item_id, state, e_total, distinct_days, distinct_sources,
  retrievals_ok, retrievals_fail, productions_ok,
  fsrs_stability, fsrs_difficulty, fsrs_last_review, due_at,
  last_encounter_at, deep_since, mastered_at, next_resurface_at,
  p_known, listening_familiarity, chosen_image_id,
  pool_entered_at, intro_complete_at,          -- ±7-day intake pool
  pending_keyed_check boolean,                 -- set by provisional promotions and flagged productions
  updated_at, version
);

-- Small daily rollup for charts and fast same-day decay lookups.
daily_item_rollup (
  user_id, item_id, local_day, passive_count, lookup_count,
  graded_ok, graded_fail, weight_sum
);
```

**Volume and practicalities.**
- A 300-word story read by one user produces ~300 passive token hits, but only **items in the programme that the user is tracking** produce events. That is roughly 20–60 events per text.
- The client **batches events per text** and syncs them in one write.
- At ~25 lessons + reading a month, expect ~3–6k events per active user per month (≈ 1–2 MB uncompressed). Keep 13 months hot in Postgres and move older raw events to cheap object storage (Parquet) for re-fitting.
- `item_state` is a projection: changing weights later means replaying events with a new `algo_version`.

## 4.8 Load control and intake (avoiding review pile-ups)

### D4.8 Intake ladder — **Recommendation — needs David's check** (adopted from David's planning brief; replaces the earlier "5 new words/day")

| Setting | New items/day | Role | Marketed? |
|---|---|---|---|
| On-ramp | 5 | First 2 weeks; auto-escalates to 10 if the escalation check passes | No |
| **Default** | **10** | The product | **Yes, headline** ("3,650 words this year") |
| Low-energy | 5 | Permanent opt-down for busy weeks | No |
| Ceiling | 15 (hard) | Unlocked only on demonstrated retention | Softly |

- **Escalation check (5 → 10, 10 → 15):** keyed-retrieval success ≥ 85% over the last 7 days, **and** yesterday's reviews fit inside the time budget below.
- **Why 10:** the brief's evidence says "5 a day" triggers Duolingo's top complaint ("too slow"). Self-directed working adults in Anki report sustaining roughly 8–50 new cards/day, and casual users around 8–10.
- **Trade-off David must accept explicitly:** content scales linearly with intake, so 10/day costs about twice the session time of 5/day. My estimate for NeuLang's lesson design (see [05](05-lessons-and-exercises.md)):
  - **≈ 17 minutes at 5/day** (one lesson block);
  - **≈ 30–35 minutes at 10/day** (two blocks, which can be done at different times);
  - ≈ 25 minutes at 10/day with production opted out.

  The brief's 56-minute figure included 16 minutes of video and 4 Textercises a day, which this plan doesn't use.
- **Instrument the real number:** if median sustained intake at week 12 is below 10, the market sits at 5 and the default flips (see [10](10-metrics-risks-roadmap.md)).

### Review budget by time, not card count
- The speed-round / keyed-review budget is **≈ 4 minutes per lesson block** (≈ 8 minutes a day at 10/day). Items left over when time runs out stay due, ordered by lowest retrievability. They're never shown as a scary count.
- **Load balancing:** when FSRS assigns an interval, pick the day with the lightest predicted load within FSRS's fuzz range, the way Anki's load balancer does. Overflow is absorbed by the schedule rather than by the learner.
- **±7-day intake pool:** new items enter a rolling pool of about 7 days × intake (≈ 70 items at 10/day). Each must get its full introduction (anchor + story appearances + keyed check) within 7 days of entering. This lets the story generator choose **topic-coherent subsets** instead of forcing 5 unrelated words into one story.
- **Backlog throttle:** new items halve when overdue items exceed 4 days' worth of review budget, and pause above 8 days' worth, with a friendly message.
- **Desired retention 0.90** by default. In settings, "Fewer reviews" sets it to 0.85 and "Stronger memory" to 0.93.
- **Return after a break:** a welcome-back session within one block's review budget, ordered by retrievability, with no new words until the backlog fits the budget.

## 4.9 Validation of the model (once there's data)
- Track **calibration**: predicted vs actual recall on graded items, per item state. Target log loss and calibration error at least as good as plain FSRS.
- Do **Deep words actually stay known?** Measure the resurfacing pass rate. Target ≥ 85%. Below that, tighten the deep criteria.
- Ablation: does passive-encounter credit improve prediction? If the HLR fit gives passive reads ~0 weight, reduce their influence.
- **AI false-accept rate** (brief's fail threshold: > 5%):
  - a native-speaker auditor labels a weekly random sample of productions the AI did *not* flag;
  - report it per learner native language;
  - also track how often provisional promotions are reversed by the next keyed review.
- **Measured encounters per target word** in each user's first 30 days, by source. This replaces the assumed figures ("3 appearances per story", etc.) that the intake model rests on.

---

## Sources
- [S1] FSRS benchmark (srs-benchmark; FSRS-6 vs SM-2 and others) — https://github.com/open-spaced-repetition/srs-benchmark and https://expertium.github.io/Benchmark.html
- [S2] `fsrs` Dart package (pub.dev; defaults: desired retention 0.9, learning steps 1 m/10 m, relearning 10 m, ratings again/hard/good/easy) — https://pub.dev/packages/fsrs
- [S3] Settles & Meeder (2016), "A Trainable Spaced Repetition Model for Language Learning", ACL — https://aclanthology.org/P16-1174/
- [S4] Uchihara, Webb & Yanagisawa (2019), "The effects of repetition on incidental vocabulary learning: a meta-analysis", *Language Learning* — https://doi.org/10.1111/lang.12343
- [S5] Webb (2007), "The effects of repetition on vocabulary knowledge", *Applied Linguistics* 28(1)
- [S6] Pellicer-Sánchez & Schmitt (2010), "Incidental vocabulary acquisition from an authentic novel", *Reading in a Foreign Language* 22(1)
- [S7] Karpicke & Roediger (2008), "The critical importance of retrieval for learning", *Science* 319 — https://doi.org/10.1126/science.1152408
- [S8] Yanagisawa & Webb, "Involvement Load Hypothesis Plus: creating an improved predictive model of incidental vocabulary learning", *Language Learning* 71(2)
- [S9] Cepeda et al. (2008), "Spacing effects in learning: a temporal ridgeline of optimal retention", *Psychological Science* 19(11)
- [S10] Hatami (2017), "The differential impact of reading and listening on L2 incidental acquisition of different dimensions of word knowledge", *Reading in a Foreign Language* 29(1), 61–85 — https://eric.ed.gov/?id=EJ1137894
- David's planning brief (2026-10-13): hard constraint on AI promotion, the two pre-scheduler questions, the intake ladder, the time-based review cap, the ±7-day pool and the re-tap demotion signal — copy at [`internal/source/neulex-planning-brief.md`](../../internal/source/neulex-planning-brief.md)
