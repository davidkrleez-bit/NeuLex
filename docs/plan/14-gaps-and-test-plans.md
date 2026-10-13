# 14 · Gaps in the plan, and how we'll test that our tests work

[← Back to plan index](../neulang-plan.md) · Plain-language walkthrough: [00 How it all works](00-how-it-all-works.md)

David asked for this section directly: *"find the gaps. find why users may feel disconnected or may lack knowledge, or how this system is not fully intuitive or easy or how this system isn't connected… create plans for how we will test whether or not our tests work."*

Everything here is **Recommendation — needs David's check**. Each gap has a fix and a priority:
- **MVP** = must be in Phase 1, because without it the core loop feels broken or untrustworthy.
- **Beta** = needed before growth.
- **Later.**

---

## 14.1 Gaps: where users may feel lost, or the system doesn't connect

### A. Things users won't understand (missing knowledge)

| # | Gap | Why it happens | Fix | Priority |
|---|---|---|---|---|
| A1 | **"You know 2,300 words" on day 1, then "0 words learned"** | Placement estimates what you already knew; the learned count only counts NeuLang's work. Two numbers that look like the same thing | **One headline number, "Words you know"**, which starts at the placement estimate and goes up as words are learned. "Learned with NeuLang: 42" is a smaller second line. The results screen says "Your NeuLang count starts today" ([03 §3.5](03-placement-test.md#35-results-screen-o10)) | MVP |
| A2 | **State names are jargon**: Candidate, Reviewing, Deep, Known (probable) | They're engineering names from [04 §4.4](04-review-model.md#44-state-machine) | **User-facing labels:** New → *New* · Learning → *Learning* · Reviewing → *Getting stronger* · Deep → *Locked in* · Mastered → *Mastered* · Known (probable) → *Already knew*. Candidate is never shown. One explainer card: "How a word gets locked in" | MVP |
| A3 | **"Why am I learning this word?"** | The ranking (frequency × interests × gaps) is invisible | **"Why this word?" chip** in triage and the Vault: "One of the 1,000 most common words" · "Common in business texts you like" · "You looked it up on Tuesday" · "A phrase English speakers use a lot" ([13 §13.8](13-interests-and-feedback.md#138-closing-the-loop-showing-users-their-interests-are-used)) | MVP |
| A4 | **"When will I see this word again?"** | FSRS schedules are invisible, and reading can reset the clock | Vault word detail: "Next check: about Thursday — sooner if it turns up in your reading" | MVP |
| A5 | **Reading doesn't feel like it counts** | Passive encounters are logged silently | End-of-text summary: "You met 4 of your words: *decide* ×3, *pollution* ×2…" plus a small "+1" pulse when a learning word scrolls past (can be turned off) | MVP |
| A6 | **The recap card's purpose is unclear** ("Nailed it / Not sure / Didn't know") | Users don't know it moves words up | Copy: "Your answer decides whether this word moves up. Be honest — it's for you." Plus the honesty check in §14.5 | MVP |
| A7 | **Phrases break in the reader.** Tapping *up* in "she gave up" shows "arriba / above" | The reader in [01 R1](01-scope-and-journey.md) taps single words | **Phrase-aware reader:** detected phrases ([12 §12.6](12-vocabulary-database.md#126-data-model-lexicon-side) `phrase_patterns`, including separable ones like "pick *the kids* up") are underlined as one unit. A tap shows the phrase meaning first, then the single word | MVP |
| A8 | **Wrong sense glossed** (*bank* of a river shown as a money bank) | Glosses are per word, not per sense | Sense-aware gloss for the ~300 split words: a sense classifier at ingest/generation, with the gloss chosen by sense. Unknown sense → show both, marked | Beta |
| A9 | **Listening "doesn't count" and nobody says so** | Listening is training, not a vocabulary channel ([04 §4.2](04-review-model.md#42-encounter-types-and-weights)) | Label the slot "Listening practice — trains your ear with words you already know". The Vault shows a separate "ear" badge for listening familiarity | MVP |
| A10 | **The time cost surprises people** | 10/day ≈ 30–35 min ([05 D5.1](05-lessons-and-exercises.md#d51-lesson-block-fixed-slots-rotating-content--recommendation--needs-davids-check)) | Show real minutes on every lesson card ("≈ 15 min"), measured per user after a week. The pace screen states the daily total | MVP |
| A11 | **A1 learners get an almost impossible text** | 98% coverage with ~300 known words leaves little to write with | **A1 track:** 3 targets per story (already planned), shorter texts, a native-language one-line summary per paragraph, more image anchors, audio on by default, social formulas first ([12 §12.4](12-vocabulary-database.md#124-ranking-words-and-phrases-on-one-scale)) | MVP |
| A12 | **English-only mode is second class** | No native-language glosses; placement uses English definitions | Allowed from B1 only (already planned); simpler-English definitions written from the top 2,000 words; a waitlist for their language | Beta |

### B. Parts of the system that don't talk to each other

| # | Gap | Fix | Priority |
|---|---|---|---|
| B1 | **The companion doesn't know what you're learning.** It chats freely, and words used there only earn E points | Send the companion a short "words to weave in" list: up to 5 items with retrievability 0.80–0.92 ([04 §4.6](04-review-model.md#46-matching-reading-to-words)). Add a **"Chat about this story"** button at the end of every story. When a chat ends, offer the same **recap card** for target words the learner used, so companion use can promote through learner authority, under the same rules ([04 §4.0](04-review-model.md)) | MVP (list + button); Beta (recap) |
| B2 | **Looked-up words vanish** | They already get ×1.5 in ranking and a speed-round slot. **Say so:** "You looked up *reluctant* yesterday — it's in today's lesson" | MVP |
| B3 | **The Vault is a dead list** | Make it actionable: "Practise these" (a filtered speed round), "Find me a text with these words" (target-density search), and "Seen in" links back to each text on the encounter timeline | Beta |
| B4 | **Feedback goes into a void** | "Got it — next text will be about X", "Because you asked for…" labels, and request status on the interests page ([13 §13.8](13-interests-and-feedback.md#138-closing-the-loop-showing-users-their-interests-are-used)) | MVP |
| B5 | **Interests change texts at once but words only slowly** | Explain the three clocks in one line ([13 §13.7](13-interests-and-feedback.md#137-how-interests-shape-texts-stories-and-words)). When an explicitly tapped topic adds words to the pool, show "New *business* words coming this week" | MVP |
| B6 | **"Articles with your words" don't say which words** | Show the word chips on the article card ("*decide*, *launch* +2") | MVP |
| B7 | **Notifications are generic** | Tie them to state: "3 words are about to fade — a 2-minute read saves them" (opens a short text with those words). One notification per day by default | Beta |
| B8 | **The speed-round cap says "the rest will wait" with no explanation** | Add: "They're scheduled for tomorrow. Skipping reviews never makes a pile-up here." Then show tomorrow's count | MVP |
| B9 | **Opting out of writing hides how words still reach "Locked in"** | The word detail shows the remaining steps: "Seen in 1 of 2 texts · 2 of 3 checks passed" | Beta |
| B10 | **Placement results and the first lesson feel unrelated** | The first lesson's words come from the gap areas found in the test, with "why" chips. The results screen previews exactly those words | MVP |

### C. Friction: places it isn't easy

| # | Gap | Fix | Priority |
|---|---|---|---|
| C1 | **Advanced users spend triage tapping "already know"** | If ≥ 3 of the first 5 triage items are "already know" + check passed, raise the user's level estimate and redraw the pool. Over 60% "already know" in a week → offer a 3-minute top-up placement | MVP |
| C2 | **Recap card on 5 words is a chore** | Only words the learner actually used appear. "Nailed it" is one tap per word, with no default, so it can't be skipped without a choice. A "same for all" button appears only when no word is flagged | MVP |
| C3 | **The feedback card after every text could annoy** | One tap, never blocking, compact mode after 5 skips ([13 §13.4](13-interests-and-feedback.md#134-the-end-of-text-review-card-every-text)) | MVP |
| C4 | **The settings list is long** (pace, writing, chat, companion, real texts…) | Group them under "Learning style" with plain-language summaries. Defaults work without touching anything | Beta |
| C5 | **A 15-minute block is long on a commute** | Blocks resume at the exact step (already planned). Add a "2-minute mode" on Today: only due speed-round cards, no new words | Beta |

### D. Gaps behind the scenes

| # | Gap | Fix | Priority |
|---|---|---|---|
| D1 | **No ground truth for "does the user know this word?"** Everything else is a model | **Audit probes:** about 1 item a day, inside the speed round, drawn **at random from all states** (including *Already knew* and *Locked in*), tested with a keyed meaning question. This unbiased sample checks the placement, FSRS, Deep and the size estimate (§14.2) | MVP |
| D2 | **No tool to fix the lexicon or content without an engineer** | Lexicon & content admin console ([12 §12.7](12-vocabulary-database.md#127-maintenance-keeping-the-database-right-over-time)) | Beta (MVP: scripts) |
| D3 | **Per-native-language calibration starts empty** | Seed with band-based priors; refit after a few hundred users per native language ([03 §3.4](03-placement-test.md#34-from-test-results-to-word-states)) | MVP (seed) |
| D4 | **A lexicon release could reshuffle the next week's pool** | Releases change ranks only for items not already in a user's ±7-day pool | MVP |
| D5 | **Story validator counts are trusted without checking** | Independent recount from reader events (§14.7) | MVP |
| D6 | **No eval harness for AI parts** | A gold-set eval suite that runs in CI on every prompt or model change (§14.5, §14.7, §14.8) | MVP |

## 14.2 How we'll test our tests: the approach

NeuLang constantly measures learners: the placement test, cloze items, the AI flagger, the recap card, FSRS predictions, the "Locked in" rule, the story validator, the interest model and the progress numbers. **Each of these is a test, and each can be wrong.** For each one, the plan sets out:
1. the **ground truth** we compare it to;
2. the **metric** and its **pass line**;
3. **when** it runs (before build, pre-launch pilot, or live);
4. **what we do** if it fails.

**Four sources of ground truth:**

| Source | What it is | Cost |
|---|---|---|
| **Audit probes** (D1) | ~1 random keyed item per user per day, from all states | ~10 s/day per user |
| **Native-speaker audits** | Paid native English speakers, plus native speakers of each supported L1 for glosses and distractors | Per sample; ~300 items per content type per audit ([06 §6.8](06-content-pipeline.md#68-qa-and-safety-chain-for-generated-content)) |
| **Pilot panel** | 150–200 paid learners per first native language (recruited through a research-panel service that can filter by first language), who also take external tests | One-off, before launch |
| **Synthetic learners** | Simulated users with a **known** vocabulary (e.g. knows exactly the top 2,000 words + 20 phrases, guesses 25%) run through the real app logic in staging | Engineering time only |

**Synthetic learners are the "test of the tests" for the plumbing.** If a simulated learner who knows exactly 2,000 words comes out of placement as "3,100", or gets taught a word it knows, the bug is in our logic, not in the learner. They run in CI for placement, the planner, the state machine and the progress numbers.

## 14.3 Test plan: the placement test

| Check | Method | Pass line | If it fails |
|---|---|---|---|
| **Recovers known sizes** | Synthetic learners (1,000 simulated, sizes 300–8,000, guessing 0–40%) through the adaptive engine | Median error ≤ 300 words; 90% within ±500; median length ≤ 40 items | Tune the stopping rule and item selection before any user sees it |
| **Distractors aren't ambiguous** | 3 high-proficiency speakers of each native language take every item; any distractor chosen by any of them, or flagged as "also correct", is reviewed | 0 unresolved items | Rewrite the item |
| **Agrees with an established test** | Pilot panel takes NeuLang placement + the Updated Vocabulary Levels Test (1k–5k bands) [03 S2] | Correlation r ≥ 0.75 | Check by band; re-weight or add items where they diverge |
| **Stable on retake** | ~50 pilot users retake it 1–3 days later | Test–retest r ≥ 0.85; median size change ≤ 250 words | More items per band, or tighter stopping |
| **"Known" really means known** | Pilot: 40 words per user that the test marked P ≥ 0.9 are checked by **typed meaning recall** (write the translation) | ≥ 90% recalled | Lower the priors in [03 §3.4](03-placement-test.md#34-from-test-results-to-word-states); require confirmation more often |
| **Calibrated in the wild** | Audit probes: bin words by predicted P(known) and compare with actual accuracy | Calibration error ≤ 0.05 per decile; "known" words later looked up or failed < 15% ([10](10-metrics-risks-roadmap.md)) | Refit 3PL parameters; adjust guessing floor |
| **Items behave** | Item analysis after ~300 users per native language: discrimination, guessing floor, distractor pick rates | Each item discriminates (point-biserial ≥ 0.2); no distractor picked by > 30% of high-ability users | Retire or rewrite the item |
| **Fair across native languages** | Differential item functioning (Mantel–Haenszel) by native language at equal ability | Large-DIF items get per-language difficulty offsets, not removal (cognates are real knowledge) | Offset in `item_l1.irt_difficulty` |
| **"I don't know" works** | Share of IDK answers; accuracy on hard items with and without IDK use | IDK used on ≥ 50% of items above the user's level; random-tapper flag confirmed by audit probes ≥ 70% of the time | Change the intro copy; adjust the random-tapping rule |
| **Short enough** | Live median duration and drop-off per item | Median ≤ 6 min; < 10% abandon during the test | Lower the max items; move stage 3 probes into the first lessons |

## 14.4 Test plan: cloze and other answer-keyed items

The cloze check is the **main promotion source** ([05](05-lessons-and-exercises.md)), so a bad cloze item directly mis-schedules words.

| Check | Method | Pass line | If it fails |
|---|---|---|---|
| **Only one right answer** | Native speakers fill a random sample of blanks **without options**; count blanks where another word fits equally well | ≤ 5% ambiguous | Tighten the LLM ambiguity pre-screen; prefer Tatoeba sentences with a stronger context |
| **The pre-screen catches ambiguity** | Compare the automated ambiguity check with the native-speaker labels above | Recall ≥ 80% on ambiguous items | Change the screening prompt/model |
| **Typed answers graded right** | 500 real typed answers (inflections, typos, US/UK spelling) labelled by hand | ≥ 98% agreement with the human label | Fix lemmatiser/typo tolerance rules |
| **Items aren't too easy to guess** | Distractor pick rates on multiple-choice cloze; accuracy of low-ability users | No distractor below 5% picks (dead distractor) | Replace dead distractors |

## 14.5 Test plan: the AI flagger and the learner's own ratings

**The AI flagger** (it can only *flag*, never promote; [04 §4.0](04-review-model.md)):
- **Gold set:** ~1,000 learner sentences per first native language (from the pilot), each labelled correct/incorrect use by **2 native-speaker raters** (agreement κ ≥ 0.7; disagreements settled by a third).
- **Metrics per native language:**
  - **false-accept** (wrong use not flagged) **≤ 5%** (the brief's fail line, [10](10-metrics-risks-roadmap.md));
  - **false-flag** (correct use flagged) **≤ 15%**, because over-flagging blocks deserved promotions and annoys.
- **Runs** in CI on every prompt or model change, and weekly on a live audited sample.

**The recap card** (learner authority):
- **Honesty check:** for each rating, compare with the **next keyed result** on that word:
  - P(correct next time | "Nailed it") should be **≥ 0.85**;
  - and clearly higher than P(correct | "Not sure").
- If "Nailed it" predicts no better than "Not sure", the ratings are noise. Change the copy or remove the rating's promotion power for that user (provisional promotions already limit the damage).
- **Provisional-promotion reversal rate**, already in [10](10-metrics-risks-roadmap.md), must stay below ~20%.

## 14.6 Test plan: the review model and "Locked in"

| Check | Method | Pass line | If it fails |
|---|---|---|---|
| **FSRS predicts recall** | Predicted retrievability vs actual keyed results (log loss, calibration curve), monthly | At least as good as default FSRS on the same data ([10](10-metrics-risks-roadmap.md)) | Refit FSRS parameters per user once they have enough reviews |
| **Passive reading boosts are real** | **Word-level randomisation inside each user:** for a random 10% of items, passive reads don't nudge stability. Compare recall at the next keyed review | Boosted items recall at least as well as control | Shrink or drop the passive boost |
| **Encounter weights** | Offline: refit weights from the event log (all events are stored, so the model can be re-run) | Fitted weights within ~2× of the guesses | Replace with fitted weights |
| **"Locked in" means locked in** | Audit probes on *Locked in* words at random times, plus the 60-day resurfacing pass rate | Resurfacing pass ≥ 85% ([10](10-metrics-risks-roadmap.md)); audit-probe accuracy ≥ 90% | Tighten the Deep criteria (more E or a longer S) |
| **Intake is sustainable** | Week-12 sustained intake; week-3-to-8 drop-off split by 5/10/15 ([10](10-metrics-risks-roadmap.md)) | Median sustained ≥ 10/day | Flip the default to 5/day |

## 14.7 Test plan: story generation and the validator

| Check | Method | Pass line | If it fails |
|---|---|---|---|
| **Validator catches problems** | **Seeded-error suite:** ~200 texts with known injected faults (a target appears only 2×, density 2.5%, coverage 94%, an off-list word, a target used in the wrong sense, too many names, a banned trope) | 100% caught for countable faults; ≥ 90% for sense errors | Fix the validator before shipping any generator change |
| **Validator doesn't reject good texts** | ~100 known-good texts | ≤ 5% false rejects | Loosen rules that misfire |
| **Counts are real** | Independent recount from reader events (which targets were on screen and dwelled on) vs the validator's stored counts | ≥ 95% agreement | Find the tokenisation or matching bug |
| **Stories are correct English** | Native-speaker audit by content type ([06 §6.8](06-content-pipeline.md#68-qa-and-safety-chain-for-generated-content)) | ≤ 2% error | Pause that content type |
| **Stories are worth reading** | 👍 rate, "felt AI" rate and read-through per text type; AI vs real excerpt | AI stories within 10 points of real texts on 👍 | Change genres/prompts; more real excerpts |

## 14.8 Test plan: interests and feedback

| Check | Method | Pass line | If it fails |
|---|---|---|---|
| **The interest model predicts likes** | For each text shown, the predicted interest match vs the 👍/👎 given; area under the ROC curve (AUC) | AUC ≥ 0.65 **and** beats a "most popular at this level" baseline | Re-weight signals in [13 §13.6](13-interests-and-feedback.md#136-how-each-signal-changes-the-users-interest-weights) |
| **Text-box parsing is right** | 300 real requests, labelled by hand with topics | Topic precision ≥ 0.85, recall ≥ 0.75; live: users delete < 15% of echoed chips | Change the parsing prompt; add taxonomy topics |
| **Interest-weighted words help** | **Offline first:** for each user, compare the coverage of texts they actually chose to read under word orders built with α = 0.7 (interests) vs α = 1.0 (general only) | Interest order gives higher coverage on chosen texts | Lower the interest share or drop it |
| | **Live A/B once there's scale** (§14.11): α 0.7 vs 1.0 on lookups per 1,000 tokens in free reading, 👍 rate, D30 retention | No harm to retention; fewer lookups | Same |
| **Feedback doesn't wear people out** | Response rate per text over weeks; share in compact mode | ≥ 40% response at week 4 | Shorten the card; ask after fewer text types |
| **Closing the loop works** | 👍 rate of "Because you asked for…" texts vs others | Higher than baseline | Fix request matching |
| **Exploration finds new interests** | Share of exploration texts that lead to a lasting weight increase | ≥ 10% | Change how adjacent topics are chosen |

## 14.9 Test plan: what users see (analytics screens)

| Check | Method | Pass line |
|---|---|---|
| **People read the numbers right** | Show the progress screen to 8–10 learners and ask: "How many words do you know? What does *Locked in* mean? What should you do next?" | ≥ 80% correct answers per question |
| **The size estimate is honest** | Audit probes vs Σ P(known) | Estimate within its own stated range for ≥ 80% of users |
| **Coverage % is honest** (shown after 6 months) | Pilot users read 3 passages and mark every unknown word; compare with predicted coverage | Error ≤ 2 percentage points |
| **The weekly recap is wanted** | Open rate | ≥ 30% |

## 14.10 Test plan: is it intuitive and connected?

- **Think-aloud sessions:** 5 learners per round, moderated, in their native language where possible. Tasks:
  - finish onboarding and placement;
  - explain why a word was chosen;
  - find when a word comes back;
  - ask for more texts about a topic;
  - say how many words they know;
  - use the companion with their words.

  Pass: each task completed without help by ≥ 4 of 5. Run a round before the pilot and after each major change.
- **Two-week diary study** (10 learners): a daily one-question prompt, "Was there a moment today you felt lost or didn't see the point?" Group the answers by gap category (A–D above).
- **Live funnels:** drop-off per screen and step ([09](09-architecture.md) events). Any step losing > 10% of users is investigated.

## 14.11 Running experiments without fooling ourselves

- **User-level A/B tests need a lot of users.** Detecting a D7 retention change from 15% to 20% (α = 0.05, power 0.8) needs about **900 users per arm**. Early on, most questions can't be answered this way.
- **So test learning mechanics within users, at the word level** (as in §14.6). Each user contributes hundreds of words, so a few hundred users give tens of thousands of observations. Analyse with mixed-effects models (random effects for user and item).
- **Write down each experiment before it starts:** hypothesis, metric, pass line, sample size and stopping rule. No peeking and stopping early on a good day.
- **Guardrails on every experiment:** D7 retention, lesson completion, false-accept rate, content error rate. An experiment that hurts a guardrail stops.

## 14.12 When each check runs

| Phase ([10](10-metrics-risks-roadmap.md)) | Checks |
|---|---|
| **Before building the feature** | Synthetic-learner simulation for placement; seeded-error validator suite; gold sets for the flagger and text-box parsing |
| **CI on every change** | Synthetic learners (placement, planner, state machine, progress numbers); validator suite; flagger and parser gold sets; cloze typed-answer set |
| **Pilot, before launch** | Pilot panel (UVLT correlation, retest, typed recall); distractor review; cloze ambiguity audit; first native-speaker content audit; 2 think-aloud rounds |
| **Live, weekly** | Content and flagger audits; feedback response rate; funnels |
| **Live, monthly** | FSRS calibration; audit-probe calibration; recap honesty; interest-model AUC; item analysis and DIF once there's enough data |
