# 11 · Distribution and positioning

[← Back to plan index](../neulang-plan.md)

Added after David's planning brief (2026-10-13; copy at [`internal/source/neulex-planning-brief.md`](../../internal/source/neulex-planning-brief.md)).
- The brief's headline: **"the pedagogy validated; the business did not."**
- Distribution scored **3/10**, the weakest result and the biggest threat.
- The brief also says: solve distribution before writing more code.

The positioning rules in §11.1 are **David decided** (from the brief), except where they conflict with project-context decisions; those conflicts are flagged. The distribution plan in §11.2–11.4 is **Recommendation — needs David's check**.

---

## 11.1 Positioning rules

| # | Rule (brief §10) | Status | How the plan applies it |
|---|---|---|---|
| 1 | Call it **automated sentence mining**, not "a vocabulary app" | David decided | Store subtitle, onboarding, paywall ([01](01-scope-and-journey.md)) |
| 2 | Lead with **the frequency system and tracked state**: "the high-frequency words **you** don't know yet" | David decided | Placement results, Vault encounter timeline |
| 3 | Cite **van Zeeland & Schmitt** for listening coverage, not Zipf's Law; state coverage; declare word families | David decided | Copy rules in [02 D2.5](02-english-lexicon-and-languages.md) |
| 4 | Podcast = **listening training** | David decided | [05 L4](05-lessons-and-exercises.md) |
| 5 | **AI chat optional; production opt-out** | David decided | [05 D5.2](05-lessons-and-exercises.md), Settings |
| 6 | Price as **$6–8/month annual or lifetime**; one-click cancel | Partly conflicts: **$8/month is David's decision** | Monthly $8 kept; annual $71.99 and limited lifetime $149 added ([08 D8.1](08-unit-economics.md)) |
| 7 | Consider **Japanese first** | **Conflicts:** project context says ESL first | ESL kept; conflict flagged in [02 §2.1](02-english-lexicon-and-languages.md) |
| 8 | **Never market an intake below 10/day** | David decided | Intake ladder ([04 D4.8](04-review-model.md)) |
| — | **Count, don't rate**: "3,650 this year"; no coverage % next to a one-month horizon | David decided | Profile, milestones, paywall copy |

**Name.** The brief calls the product **NeuLex**; the project context and the rest of this plan say **NeuLang**. Store search, domain and trademark checks depend on which name it is, so this is in the decisions list.

## 11.2 Why distribution is hard here (evidence from the brief)
- The category's comparables that tried this approach have almost no traction: ListLang **38** US ratings (stale since Aug 2024), Bukwa **3**.
- The closest functional competitor (Clozemaster, 2,036 ratings, 4.81★) and LingQ (10,275) already hold the search terms.
- **The free substitute is a ChatGPT prompt** the learner already knows how to write.
- Education apps retain poorly (D30 ≈ 2–3%; see [10](10-metrics-risks-roadmap.md)), so paid installs are expensive per retained user. The per-install contribution ceiling is only ≈ $1.20–3.00 ([08 §8.5](08-unit-economics.md#85-what-this-implies-for-growth-spend-my-estimate)), which rules out most paid acquisition at launch.

## 11.3 Channels — **Recommendation — needs David's check**

Ordered by expected cost-effectiveness for an ESL product launching in Spanish/Portuguese markets. Each channel has a measurable gate, so it's clear which ones to keep.

| # | Channel | What exactly | Why it fits | Gate metric |
|---|---|---|---|---|
| 1 | **Free web vocabulary-size test** (the placement test as a web page, in English/Spanish/Portuguese) | Same item bank as [03](03-placement-test.md). Result page: "You know ~2,300 words. Here are the 50 high-frequency words you're missing" → app install / waitlist | A shareable, search-friendly result; shows the gap-analysis USP in 5 minutes; reuses work that has to be built anyway | Test completions → email/waitlist conversion; share rate |
| 2 | **ESL creator partnerships** (YouTube/TikTok/Instagram teachers in Spanish/Portuguese markets) | Rev-share or affiliate codes; their videos become free "Watch" extras in v1.1 ([06 D6.3](06-content-pipeline.md)) | One deal gives both content and distribution; creators already own the audience | Cost per trial start vs the per-install ceiling |
| 3 | **Founder-led community presence** (r/EnglishLearning, r/languagelearning, r/Anki, r/Refold, Spanish/Portuguese learner groups) | Share the methodology honestly (intake ladder, encounter data, AI-never-promotes rule), not ads | The brief's evidence comes from these communities, and they reward transparent method posts | Referral installs tagged by source |
| 4 | **Short-form "word of the day in context" content** | Daily clips generated from NeuLang's own stories and listening podcasts (we own the rights) | Cheap to produce from the existing pipeline; shows the product | Follower → install rate |
| 5 | **App Store Optimisation in Spanish/Portuguese** | Localised listings around "vocabulario inglés", "aprender palabras en inglés", "inglês vocabulário" | Less competition than English-language keywords | Impression → install rate |
| 6 | **Referral** | Give a week, get a week | Cheap once there's a loyal base | Referral share of new trials |
| 7 | **Paid ads** | Only after channels 1–3 show which messages convert | Contribution ceiling is low | Cost per paid user < 6 months' contribution |

## 11.4 Distribution budget and sequencing — **Recommendation — needs David's check**
- **Before Phase 3** (monetisation build, see [10](10-metrics-risks-roadmap.md)):
  - launch channel 1 (web test + waitlist) as soon as the Phase 0 item bank exists;
  - start channel 3;
  - sign 2–3 creator conversations (channel 2).
- **Budget principle:** put a fixed share of build effort and cash into distribution from the start. The brief's own wording is "budget for it in the plan." Suggested starting share, for David to set: ~20–30% of founder time, and launch ad spend capped at a test budget until a channel beats the per-install ceiling.
- **Go/no-go gate for the Phase 3 build:**
  - a waitlist / web-test audience at a size David chooses;
  - at least one channel with measured cost per signup below the ceiling.

  If neither is met, fix positioning before building monetisation.

---

## Sources
- David's planning brief (2026-10-13), §3 (verdicts, H6 = 3/10), §9 (competitive reality and App Store ratings), §10 (positioning rules), §14 (case against) — [`internal/source/neulex-planning-brief.md`](../../internal/source/neulex-planning-brief.md)
- Clozemaster FAQ (sentences from Tatoeba, CC BY) — https://www.clozemaster.com/faq
- van Zeeland & Schmitt (2013), *Applied Linguistics* 34(4), 457–479 — https://doi.org/10.1093/applin/ams074
