# HANDOFF

For a new Cursor chat with **no history**. Owner: **David Lee**.

This repository is a **product plan**. The app is Flutter. **Nothing is built.** There is no code to run and no install step. Do not start coding unless David asks.

## What this is

**NeuLang** / **NeuLex** teaches high-frequency English words and phrases through repeated encounters in reading, listening and usage, instead of deck management. David's later brief frames it as *automated sentence mining*: the high-frequency words *you* don't know yet, met in context, tracked word by word.

## Dual naming

- The Project, the app, and most of this plan use **NeuLang**.
- This Origin repo, GitHub, and David's later planning brief use **NeuLex**.
- **Do not rename** the product, files, or repo until David decides. That is still open ([docs/neulang-plan.md](docs/neulang-plan.md) #8, [docs/plan/11-distribution-and-positioning.md](docs/plan/11-distribution-and-positioning.md) §11.1).

## Where the files live

- **Canonical:** this Origin repo — https://cursor.com/codebase/davidkrlee/neulex
- **GitHub:** https://github.com/davidkrleez-bit/NeuLex.git — still empty. David will push from WSL once logged in. Do not push to GitHub from this chat.

```
docs/neulang-plan.md          plan index
docs/plan/00-…14-             section files
docs/project-context.md       first batch of binding decisions
internal/                     original write-up + planning brief
```

## Start reading

1. [docs/plan/00-how-it-all-works.md](docs/plan/00-how-it-all-works.md) — how the pieces connect
2. [docs/neulang-plan.md](docs/neulang-plan.md) — every section, recommendations, open decisions
3. [docs/project-context.md](docs/project-context.md) — binding decisions from 2026-10-13
4. [internal/source/neulex-planning-brief.md](internal/source/neulex-planning-brief.md) — later brief (AI-promotion hard constraint, intake, positioning)
5. Original write-up: [internal/neulang-understanding.md](internal/neulang-understanding.md)

Tags in the plan: **David decided** = binding. **Recommendation — needs David's check** = not final until David agrees.

## Locked decisions

### From [project-context.md](docs/project-context.md) (2026-10-13)

- Flutter. Planning only. Validation skipped — we're building it.
- Launch **English as a second language** first; Spanish and French as *target* languages later.
- Lesson is a **multimodal mix that must not overwhelm**: a story, sometimes a podcast, sometimes a short YouTube section; text exercises; an opinion prompt shaped to use the target words; a picture the user describes using the word; a short newspaper / article / story excerpt; a final speed round.
- Beyond lessons: recommended articles at the user's level, articles that use their words, and AI-generated stories.
- Review: spacing close to Anki's, driven by how many times the user **encounters** a word. Enough encounters → "deep". A deep word unused for **about 2 months** comes back. Track every encounter per user with timestamps. Same-day repeats count for less.
- Content mix: real stories/news simplified by AI for lower levels; real texts as-is; stories written purely by AI. Collect a large corpus and match it to interests and level. Keep above-level reading relevant.
- **$8/month**.
- Images: picking a picture for a word is valuable; source and priority are **not** locked ([docs/plan/07-images.md](docs/plan/07-images.md)).

### Later, from David's 13 Oct questions ([plan index](docs/neulang-plan.md))

- Common **phrases** are in the programme, ranked against words by frequency, not a fixed quota ([docs/plan/12-vocabulary-database.md](docs/plan/12-vocabulary-database.md)).
- "Most common" depends on the texts the user is interested in.
- Every text ends with **"was this interesting?"**, with topic bubbles and a big text box ([docs/plan/13-interests-and-feedback.md](docs/plan/13-interests-and-feedback.md)).
- Placement: **no fake words**. Harder-and-harder real words in a sentence, with native-language multiple choice. The yes/no stage in project-context is dropped ([docs/plan/03-placement-test.md](docs/plan/03-placement-test.md)).

### From the original write-up and the planning brief (tagged **David decided** in the plan)

- 3 tabs — **Today**, **Vault**, **Profile** — plus a companion bubble ([docs/plan/01-scope-and-journey.md](docs/plan/01-scope-and-journey.md) D1.2).
- **AI never promotes an interval.** It may generate and flag; uncertainty means no promotion ([docs/plan/04-review-model.md](docs/plan/04-review-model.md)).
- Function words are never study items. Re-tapping a word marked known is the demotion signal.
- The story is the primary vocabulary engine. Cloze is its own modality. Podcast = **listening training**, not a vocabulary channel. Production is opt-out. AI chat is optional and labelled ([docs/plan/05-lessons-and-exercises.md](docs/plan/05-lessons-and-exercises.md)).
- Positioning ([docs/plan/11-distribution-and-positioning.md](docs/plan/11-distribution-and-positioning.md) §11.1): call it **automated sentence mining**, not "a vocabulary app"; lead with frequency + tracked state; cite van Zeeland & Schmitt (not Zipf); **count, don't rate**; never market an intake below 10/day.

## Still open (David needs to decide)

Copied from the end of [docs/neulang-plan.md](docs/neulang-plan.md). Do not invent closures.

1. **Intake default and its time cost.** Accept 10/day as the headline (≈ 30–35 min/day in two blocks), with a 5/day on-ramp and the week-12 metric as the tie-breaker? This is the biggest tension with "doesn't overwhelm". ([04 D4.8](docs/plan/04-review-model.md), [05 D5.1](docs/plan/05-lessons-and-exercises.md))
2. **Target language: ESL first vs the brief's Japanese-first.** This determines the whole content pipeline, so confirm before Phase 0. ([02 §2.1](docs/plan/02-english-lexicon-and-languages.md))
3. **Native languages at launch.** Spanish + Brazilian Portuguese + English-only mode, or whatever David's own audience speaks. ([02 D2.1](docs/plan/02-english-lexicon-and-languages.md))
4. **The two pre-scheduler answers.** Incorrect production = logged but not an SRS session. Textercise gate = deterministic "used in a real sentence", separate from scheduling. ([04 D4.6–D4.7](docs/plan/04-review-model.md))
5. **Distribution budget and go/no-go gate.** The share of founder time and cash, and the audience size required before building monetisation. ([11 §11.4](docs/plan/11-distribution-and-positioning.md))
6. **Content legal approach.** No scraping or simplifying of commercial news; AI fact explainers + public-domain/CC sources; lawyer review. ([06 D6.1–D6.2](docs/plan/06-content-pipeline.md))
7. **Pricing set.** $8 monthly + $71.99 annual + limited $149 lifetime; hybrid paywall with a 7-day trial. ([08 D8.1, D8.3](docs/plan/08-unit-economics.md))
8. **Product name:** NeuLang or NeuLex. ([11 §11.1](docs/plan/11-distribution-and-positioning.md))
9. **YouTube.** None in the MVP; later only free extras via creator partnerships, and a Filmot commercial-API enquiry. ([06 D6.3](docs/plan/06-content-pipeline.md))
10. **Review-model starting numbers.** Deep criteria, weights, 60/120/240-day resurfacing, listening weight 0.15. ([04 D4.2–D4.5](docs/plan/04-review-model.md))
11. **Companion fair use** (~100/day soft limit, ~300/day hard cap) and wording. ([08 D8.2](docs/plan/08-unit-economics.md))
12. **Images, programme size, backend stack.** ([07](docs/plan/07-images.md), [02 D2.5](docs/plan/02-english-lexicon-and-languages.md), [09 D9.1](docs/plan/09-architecture.md))

**New from the 13 Oct questions** (slotted by importance; the numbering above is unchanged):

- **After #1 — how much interests reshape the word list.** 70% general / 30% interests, at most 1 in 5 new items pulled forward by interests, niche words only for explicitly chosen top-2 topics. More interest weight feels more personal but risks gaps in everyday English. ([12 §12.5](docs/plan/12-vocabulary-database.md#125-how-interests-change-what-counts-as-most-common))
- **After #3 — phrase guardrails.** Max 2 phrases per block, at least 1 phrase a day from the 1,001–2,000 band up, social formulas in week 1 for beginners. ([12 §12.4](docs/plan/12-vocabulary-database.md#124-ranking-words-and-phrases-on-one-scale))
- **After #4 — the progress screen.** One headline "Words you know" (placement estimate + learned) instead of separate numbers, and plain state labels (*Getting stronger*, *Locked in*). ([14 A1–A2](docs/plan/14-gaps-and-test-plans.md#141-gaps-where-users-may-feel-lost-or-the-system-doesnt-connect), [00 §0.9](docs/plan/00-how-it-all-works.md#09-step-8--what-the-user-sees-about-their-progress))
- **After #5 — validation spend.** A pre-launch pilot panel (150–200 paid learners per first native language) plus native-speaker audits, and one ~10-second audit question per user per day. ([14 §14.2](docs/plan/14-gaps-and-test-plans.md#142-how-well-test-our-tests-the-approach))
- **After #10 — feedback card details.** Fatigue rule (compact after 5 skips), which text types get the card, and the ~15% exploration share. ([13 §13.4](docs/plan/13-interests-and-feedback.md#134-the-end-of-text-review-card-every-text), [13 §13.6](docs/plan/13-interests-and-feedback.md#136-how-each-signal-changes-the-users-interest-weights))

Brief vs recorded-decision **conflicts** (also still David's to resolve) live under "What changed after David's planning brief" in [docs/neulang-plan.md](docs/neulang-plan.md): target language, pricing shape, 10/day vs "doesn't overwhelm", the name, and how validation is described.

## What a new chat should do next

1. **Wait for David** on the list above. Do not close those items yourself.
2. **Do not start coding.** No Flutter scaffold, no backend, no MVP spike unless David asks.
3. If work continues, it is **more planning**: edit `docs/plan/` and the index. New binding answers go in [docs/project-context.md](docs/project-context.md) and the matching section.
4. Keep the dual name as-is until David picks one.
