# NeuLang — detailed product plan

Planning document for David's Flutter vocabulary app. No code. Based on:
- [project-context.md](project-context.md) (binding decisions, 2026-10-13);
- the original write-up ([understanding](../internal/neulang-understanding.md), [source](../internal/neulang-source.md));
- **David's planning brief** from another chat ([copy](../internal/source/neulex-planning-brief.md)). It adds a hard constraint on AI grading, an intake model, validation metrics and positioning rules.

The plan is long, so it is split into section files in [`plan/`](plan/). Each section lists its decisions, the options, the evidence (with numbered sources at the end of the file) and a recommendation.

## How to read the decision tags

- **David decided**: binding. Comes from project-context.md, David's original write-up (e.g. 3 tabs, triage deck), or David's planning brief (e.g. "AI never promotes an interval", podcast = listening training, function words never taught). The plan only works out the details.
- **Recommendation — needs David's check**: my proposal, backed by research. Nothing is final until David agrees.
- Costs and funnel numbers marked "my estimate" are calculated from public list prices (Oct 2026) and stated usage assumptions. Legal points are a research summary, **not legal advice**. A lawyer should review them before launch.

## Sections

| # | Section | Covers |
|---|---|---|
| 00 | [**How it all works (start here)**](plan/00-how-it-all-works.md) | Plain-language walkthrough: system diagram, where it runs, word database → placement → personal list → lesson block → story creation → spacing timeline for one word → interests → what users see |
| 01 | [Scope, user journey, screens](plan/01-scope-and-journey.md) | MVP vs later, first session and daily loop, full screen inventory incl. empty/error/settings/paywall |
| 02 | [ESL lexicon and native languages](plan/02-english-lexicon-and-languages.md) | Native languages, NGSL/NAWL/CEFR-J vs COCA, lemma vs family, coverage framing, bands + domain weighting, function words, word-selection loop, chunks, CEFR |
| 03 | [Adaptive placement test](plan/03-placement-test.md) | Harder-and-harder real words with native-language multiple choice + "I don't know" (no fake words), confirmation of lucky answers, phrase/sense probes, 3PL IRT, stopping rule, seeding word states |
| 04 | [Encounter-based review model](plan/04-review-model.md) | **Promotion authority (AI never promotes)**, the two pre-scheduler questions, FSRS + encounter hybrid, weights, same-day decay, deep state, intake ladder, time-capped reviews, ±7-day pool, data model |
| 05 | [Lessons and exercises](plan/05-lessons-and-exercises.md) | 5-item lesson blocks, story repetition/density rules, keyed cloze, opt-out production, optional AI chat, listening training, AI flagging and its validation |
| 06 | [Content pipeline](plan/06-content-pipeline.md) | Source tiers, copyright, YouTube/Filmot/podcast terms, AI explainers, difficulty, matching, QA + native-speaker audit |
| 07 | [Images](plan/07-images.md) | Scraping vs Unsplash/Pexels vs AI images, licensing, cost, priority |
| 08 | [Unit economics at $8/month](plan/08-unit-economics.md) | Cost per user at 10/day, store fees, annual/lifetime/regional pricing, trial vs freemium, companion fair use |
| 09 | [Flutter architecture](plan/09-architecture.md) | Supabase + PowerSync offline, data model, promotion invariant, LLM gateway, analytics, privacy/compliance |
| 10 | [Metrics, risks, roadmap](plan/10-metrics-risks-roadmap.md) | North star, the brief's validation metrics with fail lines, risk register, phased milestones with exit criteria |
| 11 | [Distribution and positioning](plan/11-distribution-and-positioning.md) | Positioning rules, why distribution is the biggest threat, channels with gates, budget and go/no-go |
| 12 | [Vocabulary database](plan/12-vocabulary-database.md) | Building the frequency list from human-written English, phrases and their categories, ranking words and phrases on one scale, "5 words or 4 words + 1 phrase?", interest-weighted frequency, lexicon data model, maintenance jobs |
| 13 | [Interests and feedback](plan/13-interests-and-feedback.md) | Topic and format taxonomy, onboarding bubbles + text box, the "Was this interesting?" card after every text, parsing free text, weight updates and decay, how interests shape texts/stories/words, closing the loop |
| 14 | [Gaps and test plans](plan/14-gaps-and-test-plans.md) | Where users may feel lost or the system doesn't connect (with fixes and priority), and how we test every test: placement, cloze, AI flagger, recap honesty, FSRS, "Locked in", story validator, interests, progress screens, usability, experiment design |

## One-page summary of key recommendations

1. **MVP = the core loop, done well.** Placement → 5-item lesson blocks → chip reader → Vault → paywall. The smallest test of the core thesis (frequency-ordered + context + production + SRS) ships first in the alpha.
   - The companion, listening training and images follow in the beta.
   - **No YouTube in the MVP.** YouTube's terms forbid charging users to watch embedded videos. Filmot's API isn't public and grants no commercial rights. ([01](plan/01-scope-and-journey.md), [06](plan/06-content-pipeline.md))
2. **AI never promotes an interval** (David's hard constraint). Promotions come only from:
   - answer keys (cloze, MCQ, speed round);
   - the learner's own rating on a recap card.

   The AI may only flag, and a flag means no promotion. Production-based promotions are provisional until the next keyed review. An incorrect production is logged but isn't an SRS session. The Textercise gate is a deterministic "used in a real sentence" check, separate from scheduling. ([04 §4.0](plan/04-review-model.md))
3. **Intake ladder from the brief:**
   - 5/day on-ramp → **10/day default (the headline: "3,650 words this year")**;
   - 5/day low-energy opt-down;
   - 15/day ceiling, gated on retention.

   Reviews are capped by **time** (≈ 4 min per block), not card count, with load-balancing and a ±7-day intake pool. My estimate for 10/day: **≈ 30–35 min a day in two ~15-minute blocks.** ([04 D4.8](plan/04-review-model.md), [05](plan/05-lessons-and-exercises.md))
4. **The story is the vocabulary engine.**
   - Each new target appears ≥ 3× in one coherent story, at ≤ 1.5% target density (≈ 1,000 words for 5 targets at B1).
   - Then comes a keyed cloze, using human-written Tatoeba sentences first.
   - Production is opt-out; AI chat is optional and labelled.
   - The podcast is optional **listening training**, not a vocabulary channel (Hatami 2017). ([05](plan/05-lessons-and-exercises.md))
5. **Native languages: Spanish + Brazilian Portuguese, plus an English-only mode for B1+.** ESL first stays (David decided), even though the brief floated Japanese-first. ([02](plan/02-english-lexicon-and-languages.md))
6. **Word list:**
   - frequency counted from **human-written English only** (spoken 40% / written 40% / own topic corpus 20%), checked against NGSL; function words never taught;
   - **~4,000 words + ~600–800 phrases ranked on one scale**: how much more of *your* kind of text you'll understand after learning it. This gives mostly 5 words per block at A1 and **about 4 words + 1 phrase from B1 up** (never more than 2 phrases per block);
   - **interests change the ranking**: 70% general English, 30% the user's topics, with at most 1 in 5 new items pulled forward by interests;
   - a prediction + one-tap correction loop, where **re-tapping a known word demotes it**;
   - progress shown as **counts**, with coverage % only at 6–12-month horizons. ([02](plan/02-english-lexicon-and-languages.md), [12](plan/12-vocabulary-database.md))
7. **Placement: ~5 minutes, 35–45 items, no fake words** (David decided):
   - harder and harder real words, each in a sentence with 4 native-language options + "I don't know";
   - lucky answers above the user's level are confirmed with a reverse-format item;
   - phrases and second meanings probed separately. ([03](plan/03-placement-test.md))
7b. **Interests:** bubbles + text box at sign-up, and a **"Was this interesting?" card after every text** (👎 opens topic bubbles, reasons and a big text box). Interests change texts immediately, story topics from the next block, and the word list over about a week. Visible "Because you asked for…" labels close the loop. ([13](plan/13-interests-and-feedback.md))
7c. **Gaps and test plans:** the biggest gaps found are the "2,300 known vs 0 learned" contradiction, jargon state names, a reader that breaks phrases, a companion that doesn't know your words, and no ground truth. Every measurement now has a ground truth, a pass line and a fail action, backed by one random **audit probe** a day, synthetic learners in CI and a pre-launch pilot panel. ([14](plan/14-gaps-and-test-plans.md))
8. **Content:**
   - no scraping or simplifying of commercial news. Use AI-original stories and fact-based explainers with AI labels, plus public-domain and CC sources;
   - a "Prefer real texts" setting;
   - a **native-speaker audit with a ≤ 2% error fail line**. ([06](plan/06-content-pipeline.md))
9. **$8/month still works at 10/day.**
   - A typical user costs ≈ $1.69/month (story generation + story audio is the biggest line), against ≈ $6.72 net, so ≈ 63% contribution.
   - Add an **annual plan at $71.99** ($6/month, inside the brief's range), a **limited lifetime offer at $149**, and one-tap cancel.
   - Use the cheapest passing model for the companion, plus fair-use caps. ([08](plan/08-unit-economics.md))
10. **Architecture: Flutter + Supabase + PowerSync + RevenueCat.**
    - A server-side LLM gateway.
    - The **promotion invariant is enforced in code and in CI**.
    - Privacy and compliance from day one. ([09](plan/09-architecture.md))
11. **Measure what can flip decisions:**
    - sustained intake at week 12 (median < 10 → default flips to 5);
    - drop-off vs intake;
    - AI false-accept rate (≤ 5% per native language);
    - content error rate (≤ 2%);
    - measured encounters per word. ([10](plan/10-metrics-risks-roadmap.md))
12. **Distribution is the biggest threat (3/10).**
    - Start early with a free web vocabulary test, ESL creator partnerships and transparent community posts.
    - Gate the monetisation build on a real audience. ([11](plan/11-distribution-and-positioning.md))

## What changed after David's planning brief

**Applied to the plan:**
- AI-promotion hard constraint, the recap card, and the answers to the two pre-scheduler questions (04, 05, 09).
- Intake ladder, time-capped reviews, the ±7-day pool, and lesson blocks that scale by count (01, 04, 05, 08).
- Story repetition (≥ 3×), the ≤ 1.5% density ceiling and measured target counts (05, 06).
- Keyed cloze as its own modality, with human-written Tatoeba sentences (05, 09).
- Podcast reframed as listening training with a low scheduling weight (04, 05, 06).
- Production opt-out, AI chat optional and labelled, companion can be hidden (01, 05, 08).
- Function words never taught; learner bands with domain weighting; the word-selection loop with the re-tap demotion signal (02).
- "Count, don't rate" copy rules; van Zeeland & Schmitt citation; the brief's do-not-cite list (01, 02).
- Annual $71.99 + lifetime $149 + one-tap cancel (08).
- Filmot feasibility checked (06).
- Brief metrics with fail lines; Clozemaster / ChatGPT-substitute / distribution risks (10).
- New distribution section (11).

**Conflicts between the brief and recorded decisions (not changed; David to resolve):**
- **Target language:** the brief suggests Japanese-first; project context says ESL first.
- **Pricing:** the brief favours annual/lifetime over monthly; project context says $8/month. Monthly is kept, and annual and lifetime are added.
- **"Doesn't overwhelm" vs 10/day:** the brief's linear model means ~30–35 min/day at 10/day in this design (56 min in the brief's own model, with video).
- **Name:** the brief says **NeuLex**; elsewhere it's **NeuLang**.
- **Validation:** the brief says market validation is complete, and project context says validation is skipped. These fit together: validation is done and no further validation is planned. The brief's in-product metrics are kept as post-launch checks.

## What changed after David's 13 Oct questions

**David decided:**
- Common phrases are in the programme, ranked against words by frequency, not by a fixed quota.
- "Most common" depends on the texts the user is interested in.
- Every text ends with a "was this interesting?" review, with topic bubbles and a big text box.
- No fake words in placement: harder and harder real words with multiple-choice translations.

**Applied:**
- New sections 00 (plain-language walkthrough), 12 (vocabulary database), 13 (interests and feedback) and 14 (gaps and test plans).
- 03 placement rewritten: no yes/no stage, no pseudowords; "I don't know" option, confirmation items and 3PL IRT, backed by research on guessing.
- 01: onboarding interests, new O7/O8 copy and screens, L9 after every text, phrase-aware reader, plain state labels, three headline numbers, new P5 "Your interests" page, companion fed with your words.
- 05 L9 and 06 §6.7 updated for the feedback card.
- 06 §6.8 validator now says ≥ 3× (it still said 2×).
- 09: interest and lexicon tables point to 12/13; new placement and audit-probe tables; `classify` gateway task; new events.
- 10: Phase 0/1 items for the item bank, test harnesses and MVP gap fixes.

## Decisions David needs to make (in priority order)

1. **Intake default and its time cost.** Accept 10/day as the headline (≈ 30–35 min/day in two blocks), with a 5/day on-ramp and the week-12 metric as the tie-breaker? This is the biggest tension with "doesn't overwhelm". ([04 D4.8](plan/04-review-model.md), [05 D5.1](plan/05-lessons-and-exercises.md))
2. **Target language: ESL first vs the brief's Japanese-first.** This determines the whole content pipeline, so confirm before Phase 0. ([02 §2.1](plan/02-english-lexicon-and-languages.md))
3. **Native languages at launch.** Spanish + Brazilian Portuguese + English-only mode, or whatever David's own audience speaks. ([02 D2.1](plan/02-english-lexicon-and-languages.md))
4. **The two pre-scheduler answers.** Incorrect production = logged but not an SRS session. Textercise gate = deterministic "used in a real sentence", separate from scheduling. ([04 D4.6–D4.7](plan/04-review-model.md))
5. **Distribution budget and go/no-go gate.** The share of founder time and cash, and the audience size required before building monetisation. ([11 §11.4](plan/11-distribution-and-positioning.md))
6. **Content legal approach.** No scraping or simplifying of commercial news; AI fact explainers + public-domain/CC sources; lawyer review. ([06 D6.1–D6.2](plan/06-content-pipeline.md))
7. **Pricing set.** $8 monthly + $71.99 annual + limited $149 lifetime; hybrid paywall with a 7-day trial. ([08 D8.1, D8.3](plan/08-unit-economics.md))
8. **Product name:** NeuLang or NeuLex. ([11 §11.1](plan/11-distribution-and-positioning.md))
9. **YouTube.** None in the MVP; later only free extras via creator partnerships, and a Filmot commercial-API enquiry. ([06 D6.3](plan/06-content-pipeline.md))
10. **Review-model starting numbers.** Deep criteria, weights, 60/120/240-day resurfacing, listening weight 0.15. ([04 D4.2–D4.5](plan/04-review-model.md))
11. **Companion fair use** (~100/day soft limit, ~300/day hard cap) and wording. ([08 D8.2](plan/08-unit-economics.md))
12. **Images, programme size, backend stack.** ([07](plan/07-images.md), [02 D2.5](plan/02-english-lexicon-and-languages.md), [09 D9.1](plan/09-architecture.md))

**New from the 13 Oct questions** (slotted by importance; the numbering above is unchanged):
- **After #1 — how much interests reshape the word list.** 70% general / 30% interests, at most 1 in 5 new items pulled forward by interests, niche words only for explicitly chosen top-2 topics. More interest weight feels more personal but risks gaps in everyday English. ([12 §12.5](plan/12-vocabulary-database.md#125-how-interests-change-what-counts-as-most-common))
- **After #3 — phrase guardrails.** Max 2 phrases per block, at least 1 phrase a day from the 1,001–2,000 band up, social formulas in week 1 for beginners. ([12 §12.4](plan/12-vocabulary-database.md#124-ranking-words-and-phrases-on-one-scale))
- **After #4 — the progress screen.** One headline "Words you know" (placement estimate + learned) instead of separate numbers, and plain state labels (*Getting stronger*, *Locked in*). ([14 A1–A2](plan/14-gaps-and-test-plans.md#141-gaps-where-users-may-feel-lost-or-the-system-doesnt-connect), [00 §0.9](plan/00-how-it-all-works.md#09-step-8--what-the-user-sees-about-their-progress))
- **After #5 — validation spend.** A pre-launch pilot panel (150–200 paid learners per first native language) plus native-speaker audits, and one ~10-second audit question per user per day. ([14 §14.2](plan/14-gaps-and-test-plans.md#142-how-well-test-our-tests-the-approach))
- **After #10 — feedback card details.** Fatigue rule (compact after 5 skips), which text types get the card, and the ~15% exploration share. ([13 §13.4](plan/13-interests-and-feedback.md#134-the-end-of-text-review-card-every-text), [13 §13.6](plan/13-interests-and-feedback.md#136-how-each-signal-changes-the-users-interest-weights))
