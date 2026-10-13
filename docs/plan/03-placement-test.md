# 03 · Adaptive placement test

[← Back to plan index](../neulang-plan.md) · How we'll check it works: [14 §14.3](14-gaps-and-test-plans.md#143-test-plan-the-placement-test)

**David decided:**
- The test is quick and adaptive. It goes easy → hard, then digs into the clusters, topics and bands where the user shows gaps.
- Its main format is a word in an English sentence followed by a **multiple-choice meaning question in the native language**.
- **(2026-10-13) No fake words.** The earlier design mixed made-up "pseudowords" into a yes/no list to catch overclaiming. David rejected this. Instead the test **keeps asking harder and harder real words**, with **multiple-choice translations**. The yes/no stage is dropped too, since without fake words it can't detect overclaiming.

Everything below is **Recommendation — needs David's check**: how to make that design accurate, short and hard to fool.

---

## 3.1 What the test must output

The test isn't just a level badge. It must output:
1. A **P(known)** prior for every item in the ~4,000-word + ~700-phrase programme ([12](12-vocabulary-database.md)). This decides what not to teach and seeds the review model's starting states.
2. A **vocabulary size estimate with an uncertainty range**, which feeds the coverage % and the vocabulary CEFR label ([02 §2.4](02-english-lexicon-and-languages.md#24-cefr-mapping)).
3. **Gap areas**: topics, phrase types and senses where the user knows less than their size predicts (e.g. strong general vocabulary, weak phrasal verbs). These seed the first weeks of new items.

## 3.2 Evidence that shapes the design

- **Bilingual multiple-choice works well, especially at low levels.** The Vocabulary Size Test (VST, 14 × 1,000-word bands) has bilingual versions recommended for lower-level learners [S1]. The Updated Vocabulary Levels Test samples the 1k–5k bands [S2]. David's format (sentence + native-language options) is this format.
- **The weakness of multiple choice is guessing.** With 4 options, a blind guess is right 25% of the time, and informed guessing (eliminating options) is right more often:
  - Gyllstad, Vilkaitė & Schmitt (2015) compared VST answers with interviews: **11–26% of items credited as correct weren't actually known**, more than blind guessing alone explains. They also found that ~10 items per 1,000-word band is too few for a stable estimate [S3].
  - Stewart (2014) showed that multiple-choice options inflate VST size estimates [S4].
- **An "I don't know" option reduces guessing.** Zhang (2013) randomly assigned 150 learners to VST versions with and without "I don't know". The option **reduced guesses and slightly improved reliability**, but also lowered scores by removing credit for partial knowledge [S5]. Nation's VST notes say the same: with "I don't know", scores move closer to true recall [S6].
- **IRT models with a guessing term fit multiple-choice vocabulary data better.** A validation study found the 3-parameter logistic model (3PL, which includes a "lucky guess" floor) fitted the VST better than 1- or 2-parameter models [S7].
- **Adaptivity cuts length a lot.** A computer-adaptive vocabulary-size test reached comparable estimates using about a third of its item bank [S8].
- **Cognates bias item difficulty by native language** [S9]. Item difficulty must be calibrated per native language, and false friends (*embarrassed* vs Spanish *embarazada* "pregnant") are good diagnostic items.

**What this means for NeuLang.** Our cost of an error is lopsided:
- **Wrongly marking a word "known"** means it's never taught. That's the expensive mistake.
- **Wrongly marking a word "unknown"** means it shows up in triage, where the user taps "already know" and passes a 2-second check (L1). That's cheap.

So we want a **tough** test, not a generous one. That's exactly the case where the research says: include "I don't know", and model guessing explicitly.

## 3.3 Test design (about 5 minutes, 35–45 items)

### The item

> **She was *reluctant* to leave.**
> What does ***reluctant*** mean?
> ○ *[native language]* unwilling
> ○ *[native language]* excited
> ○ *[native language]* able
> ○ *[native language]* afraid
> ○ **I don't know**

- **The sentence** shows the part of speech and the sense but **doesn't give the meaning away** (VST style: "She was *reluctant*…", not "She didn't want to go; she was *reluctant*").
- **4 options** in the native language, plus **"I don't know"** as a 5th, visually distinct button.
- **Distractors** (wrong options) are:
  - translations of other English words of the **same part of speech and frequency band**, so the right answer can't be guessed by "it's the only verb" or "it's the only common word";
  - where possible, **one look-alike trap**: the translation of a similar-looking English word (*affect* → include *effect*'s meaning) or a false friend (*actually* → include Spanish *actualmente* "currently");
  - checked so that **no distractor is also a correct translation** (LLM check + native-speaker review).
- **English-only mode:** 4 short English definitions written only with top-2,000 words (UVLT style).
- **Soft timer:** none visible. The response time is logged.
- **Copy before the test (O7):** "Pick *I don't know* instead of guessing. Guesses make your lessons too hard or skip words you need."

### D3.1 Stage 1 — the ladder: harder and harder words (≈ 2.5 minutes, 18–24 items)
- **Item bank:** programme words grouped into **500-word frequency bands** (1–500, 501–1,000, … 4,501–5,000), plus "5k–8k" and "beyond 8k" bands for advanced users.
- **Start** two bands below the self-rated level (O6), so the first few answers feel easy.
- **Climb:**
  - **2 correct in a band → jump up** (2 bands at first, then 1 band once the user misses anything);
  - **a miss or "I don't know" → stay** and ask one more in that band;
  - **2 misses in a band → step down one band** and narrow in.
- From item ~10 on, a **3PL IRT model** picks the next word: the most informative word near the user's current ability estimate, from a band not yet over-sampled. To the user it still looks like "the words keep getting harder until they're about right".

### D3.2 Stage 2 — confirmation of lucky answers (≈ 1 minute, 5–8 items)
This replaces the fake-word check. It targets the specific risk of guessing.
- **Any correct answer in a band above the user's estimated level** gets a **second item from the same band**, in the **reverse format**: native-language word → pick the English word from 4 English options. A lucky guess rarely survives two different formats.
- **Very fast correct answers on hard items** (< ~1 s, faster than reading the options) are also confirmed.
- If a user's answers on hard items look random (≈ 25% correct with no "I don't know"), the model's guessing estimate for that user goes up, and the result screen says: "Some of those looked like guesses — no problem, we'll double-check those words in your first lessons."

### D3.3 Stage 3 — gap probes: phrases, senses, topics (≈ 1.5 minutes, 10–12 items)
Same item format, chosen where the model is **most uncertain and most useful**:
- **3–4 phrases**, in a sentence: "What does ***come up with*** mean here?" Single-word knowledge overestimates phrase knowledge ([12 §12.3](12-vocabulary-database.md#123-phrases-what-counts-where-they-come-from-how-theyre-categorised)), so phrases are tested separately and get their own estimate.
- **2 senses of common polysemous words** (*run a company*, *a bank of the river*), since knowing the first sense doesn't mean knowing the second.
- **2 false-friend or cognate items** when the native language has them.
- **2–3 words from the user's interest topics** ([13](13-interests-and-feedback.md)), to calibrate topic gaps and make the test feel personal.

### D3.4 Optional listening check (v1.1)
3 items: hear a word in a sentence and pick its meaning. Learners are often much weaker at listening than reading, so this sets a separate "listening factor" for choosing audio difficulty.

### D3.5 Stopping rule
Stop when the standard error of the size estimate is below ~±300 words, **or** at 45 items, **or** at 6 minutes, whichever comes first, with a **minimum of 25 items** so one lucky streak can't end it. A "Skip, I'll learn as I go" link after stage 1 gives a coarse estimate and marks the profile as low-confidence, to be refined passively.

## 3.4 From test results to word states

| Evidence for a word | Initial state | Prior P(known) |
|---|---|---|
| Correct, and confirmed by a second item or a different format | **Known (probable)**: not taught; spot-checked later ([04 D4.4](04-review-model.md#d44-resurfacing-deep-words--david-decided-about-2-months-details-are-a-recommendation)) | 0.95 |
| Correct, in a band at least 1 below the ability estimate | Known (probable) | 0.90 |
| Correct, but at/above the ability estimate and **not** confirmed | **Watch**: eligible for triage, where "already know" + the quick check resolves it in 2 seconds | 0.6 |
| "I don't know" or wrong | **Candidate**: eligible for teaching | 0.1 |
| Untested | From the IRT curve for its band (and per-native-language difficulty offset) | 0.05–0.98 |
| Phrase untested | From the **phrase** estimate, not the word estimate | Usually lower than words in the same band |

- **Size estimate shown to the user** ≈ Σ P(known) over the top 5,000 words, with a range ("about 2,300, give or take 300").
- **Passive re-estimation after the test:**
  - each lookup tap on a "known (probable)" word lowers its P (and may make it a candidate);
  - reading through it without a tap raises it a little;
  - triage "already know" + passing the quick check (L1) confirms it.

  The estimate updates continuously, so there's no need to retest.
- **Item calibration loop.** Store every response with the user's native language and response time. Once there's data from a few hundred users per native language, refit the 3PL item parameters per native language (difficulty, discrimination, guessing floor) and replace the band-based starting values. Items whose distractors attract too many strong users are rewritten ([14 §14.3](14-gaps-and-test-plans.md#143-test-plan-the-placement-test)).

## 3.5 Results screen (O10)
- Headline: "You know about **2,300** of the most common English words" with "give or take 300" underneath.
- Coverage: "That's **~85%** of the words in everyday English. Most people feel comfortable at 95%."
- Vocabulary level badge (e.g. "≈ A2 vocabulary"), with an ⓘ explaining what it means.
- **Phrases line:** "You know fewer common phrases than words — we'll mix those in." This explains in advance why phrases appear early.
- "Your first words" preview: 5 items from the gap areas, each with its "why" ("very common", "common in tech texts you like").
- **This number isn't "words learned with NeuLang".** That counter starts at 0 (see [14 §14.1](14-gaps-and-test-plans.md#141-gaps-where-users-may-feel-lost-or-the-system-doesnt-connect), "two numbers" gap). The screen says so: "Your NeuLang count starts today."
- If confidence is low: "We'll fine-tune this as you read."

## 3.6 Item bank and edge cases
- **Bank size per native language:** about 25 single-word items per 500-word band × 12 bands (≈ 300), plus ~60 phrase items, ~40 sense items and ~20 false-friend items: **about 420 items**. Each needs 4 translated options checked by a native speaker. This is real content work for every native language added ([02](02-english-lexicon-and-languages.md)), and it's in the decisions list as part of "which native languages first".
- **Random tapping** (fast answers, ~25% accuracy, no "I don't know") → the guessing estimate goes up, more items are confirmed, and the copy in D3.2 is shown.
- **All "I don't know"** → start at A1, with a quick upgrade if the first lessons' triage says "already know" a lot.
- **Advanced user** (stage 1 tops out) → probe the 5k–8k band and academic words. An honest message if NeuLang's core programme is too easy: "You'll mostly use Read and Companion."
- **App killed mid-test** → resume from the last answered item. The item bank ships in the app, so the test works offline.
- **Retake** (Profile → Settings) is allowed once every 30 days. It updates the priors of untaught items only; it never overwrites the state of a word NeuLang has taught.

---

## Sources
- [S1] Nation & Beglar (2007), "A vocabulary size test", *The Language Teacher* 31(7); bilingual versions — https://www.wgtn.ac.nz/lals/resources/paul-nations-resources/vocabulary-tests
- [S2] Webb, Sasao & Ballance (2017), "The updated Vocabulary Levels Test", *ITL* 168(1)
- [S3] Gyllstad, Vilkaitė & Schmitt (2015), "Assessing vocabulary size through multiple-choice formats: issues with guessing and sampling rates", *ITL* 166(2), 278–306 — https://doi.org/10.1075/itl.166.2.04gyl
- [S4] Stewart (2014), "Do multiple-choice options inflate estimates of vocabulary size on the VST?", *Language Assessment Quarterly* 11(3), 271–282
- [S5] Zhang (2013), "The *I don't know* option in the Vocabulary Size Test", *TESOL Quarterly* 47(4), 790–811 — https://doi.org/10.1002/tesq.98
- [S6] Nation (2012), "The Vocabulary Size Test: information and specifications" — https://www.wgtn.ac.nz/lals/resources/paul-nations-resources/vocabulary-tests/the-vocabulary-size-test/Vocabulary-Size-Test-information-and-specifications.pdf
- [S7] Gökcan & Çobanoğlu Aktan (2022), "Validation of the Vocabulary Size Test", *Journal of Measurement and Evaluation in Education and Psychology* — 3PL fitted better than 1PL/2PL; the guessing effect was "not negligible"
- [S8] Computer-adaptive vocabulary size test using a fraction of its item bank, *Computers & Education* (2016)
- [S9] Elgort (2013), "Effects of L1 definitions and cognate status of test items on the Vocabulary Size Test", *Language Testing* 30(2), 253–272 — https://doi.org/10.1177/0265532212459028
