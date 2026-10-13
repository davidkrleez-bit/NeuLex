# NeuLex — Planning Brief

**For:** a planning/architecture model with no prior context.
**Purpose:** everything needed to produce a build plan for NeuLex.
**Status:** market validation complete. **Nothing is built.** No application code exists yet.

> **Read §5 and §6 before proposing anything.** §5 is a hard constraint that the plan must not
> violate; §6 lists the two questions that must be answered before the scheduling layer is designed.

---

## 1. What NeuLex is

**One sentence:** NeuLex automates sentence mining — it teaches frequency-ordered vocabulary
through varied comprehensible input, retrieves it through production, and schedules each word
individually.

**It is not** a flashcard generator, a Duolingo clone, or a fixed-5-words-per-day app.

**Settled product definition.** A daily session with five modalities:

| Modality | What it is | Function |
|---|---|---|
| **Fill in the blank** | Cloze items | Cued recall |
| **Story** | A short narrative containing the day's target words | Reading comprehension — **the primary vocabulary engine** |
| **Podcast** | Audio version / separate listening piece | Listening comprehension — *listening training*, explicitly **not** a vocabulary channel |
| **Q&A** | Open-ended questions answered trying to use given words | Open-ended production |
| **Textercise** | AI chat where the learner must use all target words to finish the lesson | Gated production |

**Mechanism details that are load-bearing:**

- **A successful production counts as a spaced-repetition session** for that word and updates its
  interval. Production is not a side activity; it feeds the scheduler.
- The chat shows the target-word list; the AI is biased toward in-study words.
- **Function words appear naturally in the text but are never study items.** They are acquired
  implicitly. This is not "filtering them out" — it is declining to make them targets.
- **Intake is adjustable.** Session time and content volume scale with it (see §4).
- **Content is personalised by gap, not by list:** the app teaches *the high-frequency words this
  learner does not yet know*, ordered by frequency and by the learner's domain/interest weighting.

**Correct external characterisation:** *automated sentence mining*. Lead with the frequency system
and the tracked learner state — never "flashcards."

---

## 2. The problem it addresses

Evidence-backed failure modes of the incumbents:

- **Anki's failure mode is too much, not too little.** r/Anki's own top post (378↑): *"if they keep
  learning tons of new cards, after a few months they will have to do so many reviews that it will
  become overwhelming, making them quit."* Steady-state reviews ≈ new cards/day × ~10, so 50 new/day
  becomes a ~500-review queue.
- **Duolingo's failure mode is too slow.** r/duolingo 444↑/156c: *"one 5 minute lesson a day… it
  would take 16.4–19.7 years."* 319↑/67c: *"5 minutes a day… is just not enough time to get your
  brain to switch gears."*
- **Sentence mining works but is manual.** A B2 polyglot doing it by hand (902↑): *"I made a prompt
  so that I just write the word… I then add the sentence to an Excel spreadsheet which I then import
  from Anki."* Four tools and a spreadsheet.

NeuLex's thesis: keep sentence mining's effectiveness, remove its labour, and cap the load so it
survives month three.

---

## 3. Validated verdicts

Eight hypotheses were tested against Reddit, Hacker News, App Store data, and peer-reviewed
research. Scores are on a 0–10 confidence scale.

| # | Hypothesis | Score | What it means for planning |
|---|---|---|---|
| H1 | Automated sentence mining: respected practice, real demand | **6/10** (practice 9, commercial 4) | Method is endorsed; **commercial proof is weak** |
| H2 | Multi-modal exposure improves retention | **6/10** | Directionally supported; three specific fixes required (§7) |
| H3 | AI reliably corrects learner output | **5/10** | Risk is **HIGH for interval promotion** — drives §5 |
| H4 | The comprehension claim is defensible | **7/10** | Strongest hypothesis; use the sourced framing (§11) |
| H5 | Learners will pay a subscription | **5/10** | One-time purchase beats subscription; $5–12/mo is risky |
| H6 | Distribution is achievable | **3/10** | **The weakest result and the biggest threat** |
| H7 | Learners want high-frequency words taught | **7/10** | Validates the frequency-ordered core |
| H8 | The SRS/intake model holds up | — | Modelled; see §4 |

**Headline: the pedagogy validated; the business did not.** Every design hypothesis scored 5–7.
Distribution scored 3.

---

## 4. The intake model

### 4.1 Recommended ladder

| Setting | Rate | Role | Marketed? |
|---|---|---|---|
| On-ramp | **5/day** | Weeks 1–2, auto-escalates to 10 | **No** |
| **Default** | **10/day** | The product | **Yes — headline** |
| Low-energy | 5/day | Permanent opt-down for busy weeks | No |
| Ceiling | 15/day (hard) | Gated above on demonstrated retention | Softly |

**Never market an intake below 10/day.** "5 words a day" reads as the Duolingo "too slow" objection,
which is the single most-upvoted complaint about the incumbent.

### 4.2 Why 10/day is defensible

From the r/Anki self-report thread (`/r/Anki/comments/1btjfhm/`), new-cards-per-day splits by stakes:

- **Exam cram** (med/MCAT): 120–400/day — not this market
- **Language learners with jobs:** 20–50/day
- **Casual / for-fun learners: 8–10, 8, 2, ~3 per week** — *at or below the default already*

So 10/day is not a constraint imposed on the market; it is the top of what self-directed working
adults actually sustain. r/Anki already upvotes the framing: *"6k deck, 10 new cards a day, zero
burnout"* (33↑).

### 4.3 Session cost — CORRECTED model

⚠️ **The H8 analysis file contains a superseded model.** It assumed a **fixed-size** story and
concluded session time was flat (~21 min) at every intake and that 10/day was "marginal." That
assumption was wrong: **content scales by count.** The corrected figures:

| Intake | Session time | Encounters/word | Content per day |
|---|---|---|---|
| **5/day** | ~28 min | 8.8 | 1 story, 2 clips, 2 textercises |
| **7/day** | ~45 min | **10.4** (best ratio) | — |
| **10/day** | **~56 min** | 8.8 | **2 stories, 4 clips, 4 textercises** |

At 10/day the ~56 min breaks down roughly: 14 min reading, 16 min video, 16 min textercise,
~2 min cloze, ~8 min flashcards. **100% of words are met in context** — no word is flashcard-only.

Weekly at 10/day: 14 stories, 28 clips, 28 textercises, 70 cloze items, ~19,600 story words.

**Key properties of this model:**

- **It is linear.** 2× the words costs 2× the time. There is no efficiency gain from higher intake.
- **Production must be capped per session and rotated**, or 4 textercises/day becomes a wall.
- **Review must be capped by TIME, not card count.** A fixed review budget pushes overflow into the
  scheduling window rather than into user pain.
- **±7-day scheduling pool at 10/day = 70 words.** A word does not need its full treatment on one
  specific day.

### 4.4 Story capacity is the binding constraint

Story target density must stay near **~5 targets per ~1400-word story** (hard ceiling ~7). Above
~1.5% target density the narrative degrades into "a word list with prose."

**More words means more stories, not longer ones.** This makes content generation the scaling cost —
a COGS problem, not an engagement problem.

### 4.5 Encounter targets

From Webb (2007), *Applied Linguistics* 28(1), 46 — controlled study, 121 learners, 1/3/7/10
encounters: *"If learners encounter unknown words ten times in context, sizeable learning gains may
occur. However, to develop full knowledge of a word more than ten repetitions may be needed."*
A meta-analysis puts the encounter-frequency correlation at only **0.23–0.43**, with **no fixed number
guaranteeing learning**.

**Design against ~8–12 encounters**, treating 10 as a floor for partial knowledge rather than a
mastery target.

---

## 5. HARD CONSTRAINT — the one rule that must not be violated

> **The AI may generate and may flag. It must NEVER be the sole authority that promotes a
> spaced-repetition interval.**
> **Uncertainty resolves to NO promotion.**
> **A bare "correct!" must never lengthen a card.**

**Why:** documented AI failures cluster precisely on *meta-judgement about correctness* ("is this
sentence right?"), they are confident, and the error is **asymmetric** — a false accept silently
lengthens the interval on the learner's weakest word. That is a mechanism for confidently teaching
errors.

**Implication for the plan:** the grading loop must keep the learner or an answer key in authority.
AI output may inform a decision; it may not *be* the decision. Any design where the Textercise AI
alone advances a word's interval is rejected.

---

## 6. Two questions to answer BEFORE designing the scheduler

Both concern stored state, both are cheap, and both change the data model:

1. **Does an incorrect production still count as an SRS session?**
   If yes, it corrupts stored learning state. Decide explicitly.
2. **Is the Textercise gate "all target words appeared" or "all target words were used correctly"?**
   If the former, the guard is far weaker than the design implies.

---

## 7. Three fixes H2 requires

The multi-modal design is supported but needs these, or it is exposed:

1. **Make production opt-out, not mandatory.** 694↑: *"a common mistake to waste time producing
   output at the beginner levels."*
2. **Reframe the podcast as listening training, not a vocabulary channel.** **Reading does not
   transfer to listening** — 6+ high-engagement threads, and Hatami (2017) found readers outperform
   listeners on all five word-knowledge dimensions, with the frequency-of-occurrence benefit holding
   for reading but *not* listening.
3. **Guarantee high-frequency repetition of each target inside one coherent text.**

Also: **make the AI chat optional and label it clearly.** The paying segment is hostile to mandatory
AI chat — 1.6k↑/450c: *"PLEASE DO NOT use AI for chatting practice."* The category's most-upvoted new
entrant sells itself on having *"no ai content"* (1.6k↑).

---

## 8. Open decisions for the planning model

These are unresolved and are the planner's to work through:

1. **Language market.** Evidence suggests **Japanese-first** over Spanish: r/Refold (125↑) argues
   sentence mining's value is Japanese-specific, because European languages have direct translations
   and free frequency decks that *"do an amazing job with little work."* This determines the entire
   content pipeline, so decide before designing it.
2. **MVP scope.** Which modalities ship first? The five-modality model is the *validated* product,
   but shipping all five is a large surface. Recommend identifying the minimum that still tests the
   core thesis (frequency-ordered + context + production + SRS).
3. **Scheduler design.** FSRS-style vs SM-2; how production events and review events unify into one
   per-word interval; how the ±7-day flexibility window interacts with scheduling.
4. **Word-selection loop.** The intended flow is a prediction model for unknown words plus a
   low-friction correction loop (the learner taps to fix a wrong prediction). Needs designing:
   cold-start, confidence handling, and the demotion signal.
5. **Granularity.** A flat "top 3000" is explicitly rejected as too coarse. Needed: bands or
   sub-bands that are meaningful to a learner, weighted by domain/interest so frequency varies by
   field.
6. **Content generation pipeline.** Story/video/cloze production at 14 stories + 28 clips per week at
   10/day. This is the scaling cost — plan it as a pipeline with unit economics.
7. **Video sourcing.** A YouTube clip search (3–5 min containing ~5 target words) is desirable and
   plausibly feasible: **Filmot** indexes ~1.66B transcripts with an API supporting duration filters
   and timestamped keyword hits. Verify feasibility rather than assuming.
8. **Distribution.** Scored 3/10 and was never solved. Budget for it in the plan.

**Explicitly rejected — do not propose:** a loop that requires learners to produce a sentence for
every word to "prove" acquisition, with AI demoting words. The founder rejected this as *"a logistical
nightmare."* Not every word must be practiced to be used. The cheap demotion signal is **re-tapping a
word previously marked known** — no production proof required.

---

## 9. Competitive reality

**Closest functional competitor: Clozemaster** — frequency-ordered, real context, SRS, mobile, free
tier, **human-written** sentences. 2,036 ratings, 4.81★. It sidesteps the authenticity objection
entirely. Not Bukwa, which is effectively dead.

US App Store traction (`itunes.apple.com/lookup`):

| Product | Stars | Ratings |
|---|---|---|
| LingQ | 4.74 | 10,275 |
| AnkiMobile | 4.04 | 2,342 |
| **Clozemaster** | 4.81 | **2,036** |
| Migaku | 4.38 | 366 |
| ListLang | — | **38** (stale since Aug 2024) |
| Bukwa | — | **3** |

**The free substitute is the product.** Learners already prompt ChatGPT to embed target vocabulary on
a spaced schedule. NeuLex's only durable advantage — tracked state — is a feature, not a moat.

**Pricing evidence:** one-time purchase outperforms subscription in this segment — *"$25 up front is
a million times better than a $4 monthly subscription."* Recommended: **$6–8/mo annual, or lifetime
$50–199**, with one-click cancellation.

---

## 10. Positioning rules

1. Call it **automated sentence mining**, not "a vocabulary app."
2. Lead with **the frequency system and tracked state** — *"the high-frequency words **you** don't
   know yet"* (gap analysis, not a list).
3. Cite **van Zeeland & Schmitt (2012)**, not Zipf's Law. State **coverage**; declare **word
   families**.
4. Reframe the podcast as **listening training**.
5. Make **AI chat optional**; make **production opt-out**.
6. Price as **$6–8/mo annual or lifetime**; one-click cancellation.
7. Consider **Japanese first**.
8. **Never market an intake below 10/day.**

### Copy-ready framing (verified arithmetic)

| Rate | /week | /month | /year |
|---|---|---|---|
| 5/day | **35** | ~150 | 1,825 |
| **10/day** | **70** | ~300 | **3,650** |

**Count, don't rate.** "10 a day" feels small; "3,650 this year" feels like progress.

**Never put a coverage percentage next to a one-month horizon** — the coverage curve is logarithmic
(steep early, flattening hard), and a month's delta is small and falsifiable. Word counts for months;
coverage percentages only at 6–12 month horizons.

---

## 11. Reference numbers (verified)

| Threshold | Figure | Source |
|---|---|---|
| 98% written coverage | 8,000–9,000 word families | Nation (2006) |
| 98% spoken coverage | 6,000–7,000 families | Nation (2006) |
| **95% spoken = adequate LISTENING** | **2,000–3,000 families** | **van Zeeland & Schmitt (2012)** |
| Minimal reading | 4,000–5,000 families ≈ 95% | Laufer & Ravenhorst-Kalovski (2010) |
| First 1,000 families | ~78–81% of written text | BNC/COCA lists |
| First 2,000 families | ~83% | BNC/COCA lists |
| Encounters for sizeable gains | 10; >10 for full knowledge | Webb (2007) |
| Steady-state review load | new/day × ~10 | Anki manual |

⚠️ **Do not cite:** "1,000 words → 80% coverage / 55% comprehension" (retracted — the 55% is likely a
Laufer 1989 passing test score, not comprehension); "audio ≈ half the vocabulary yield of reading"
(unsourced folklore).

⚠️ **Unmeasured modelling assumptions** carried into §4: "3 appearances per story target," "2 per
video," "3 targets per clip." **These were assumed, never instrumented.** If stories show each target
once, all encounters-per-word figures drop by roughly two-thirds and the 10/day case weakens
materially. **Instrument this early** — it validates or invalidates the whole intake model.

---

## 12. What to produce

A build plan covering, at minimum:

1. **Architecture** — content pipeline, scheduler, learner-state model, AI surfaces.
2. **The scheduler data model**, answering §6 first and honouring §5.
3. **MVP scope** — which modalities ship, and what the smallest test of the core thesis is.
4. **Content pipeline design** with unit economics (the real scaling cost).
5. **Instrumentation plan** — encounter counting (§11), correction error rate, and the metrics below.
6. **A sequencing of work**, with the language-market decision (§8.1) resolved early because it
   determines the content pipeline.

### Metrics the validation says to watch

- **Actual sustained intake at week 12 by cohort.** If median < 10, the market genuinely sits at 5 —
  and that would flip the intake recommendation.
- **Week-3-to-8 drop-off vs intake rate.** The Anki literature predicts drop-off rises with intake.
  If NeuLex shows drop-off **flat across 5/10/15**, that is a defensible claim no competitor can make.
- **AI correction false-accept rate**, in each target language separately. **>5% is a fail** — it
  means the §5 risk is live in production.
- **Content error rate** by native-speaker audit. **>2% is a fail.**
- **Measured encounters per target word**, replacing the assumed figures in §11.

---

## 13. Where the evidence lives

| File | Contents |
|---|---|
| `HANDOFF.md` | Validation handoff — verdicts, risks, experiments, file map |
| `validation-rerun/RERUN-REPORT.md` | **Main report** — H1–H6 in full, source index (~630 lines) |
| `validation-rerun/03-H8-srs-load-and-intake-model.md` | SRS/intake modelling — ⚠️ **contains the superseded fixed-size model; §4.3 above corrects it** |
| `validation-rerun/02-H7-high-frequency-words.md` | H7 verdict |
| `validation-rerun/04-H9-five-vs-ten-persuasion.md` | Why 5/day isn't marketable; Anki usage distribution |
| `validation-rerun/ONBOARDING-FRAMING-REFERENCE.md` | Copy-ready onboarding/paywall blocks + honesty constraints |
| `research_SYNTHESIS.md` | Cross-source synthesis |
| `validation/` | **Run 1 — SUPERSEDED.** Tested a mis-described product; do not cite. |

---

## 14. The strongest case against, at full strength

Stated plainly so the plan accounts for it rather than assuming it away:

> The pedagogy is sound and the business is not. The comprehension target is right when measured
> against listening research; the multi-modal design is endorsed by a peer-reviewed paper; sentence
> mining is the community's default method. But the closest competitor already occupies the exact
> functional space with human-written content and 2,036 ratings; the comparables that tried this
> have ~zero traction (38 and 3 ratings); the free substitute is a ChatGPT prompt the learner
> already knows how to write; and distribution — the thing that actually decides whether anyone
> uses it — scored 3/10 and remains unsolved.

**The risk is not pedagogical, and the answers are empirical:** measure the correction error rate,
audit the content, keep the AI out of the promotion decision, instrument the encounter counts, and
solve distribution before writing more code.
