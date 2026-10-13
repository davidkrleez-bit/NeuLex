# 10 · Metrics, risks and phased roadmap

[← Back to plan index](../neulang-plan.md)

**David decided:** validation is skipped. NeuLang is being built. The roadmap therefore uses **build milestones with measurable exit criteria** rather than validation experiments. The metrics below act as the in-product validation once real users arrive. Everything here is **Recommendation — needs David's check**.

---

## 10.1 Metrics

### North-star metric
**Weekly learned-and-retained items per active learner**: items that moved into Reviewing/Deep/Mastered and passed their last graded check. It measures what NeuLang promises (words that stick), not just activity.

### Product metrics and targets

| Metric | Definition | Benchmark | NeuLang target (first cohorts) |
|---|---|---|---|
| Onboarding completion | Install → placement done | — | ≥ 70% |
| First-lesson completion | Placement done → lesson 1 done | — | ≥ 75% |
| D1 / D7 / D30 retention | Opened app and did ≥ 1 learning action | Education apps ≈ 14–15% / ~6% / 2–3% [S1]; some datasets give D30 ≈ 8% | ≥ 30% / 15% / 8% for trialists |
| Trial start rate | Installs → trial | Global ≈ 10.9% [S2] | ≥ 12% |
| Trial-to-paid | Trials → paid | Median 37.4% for 5–9-day trials; North America 34.2% [S3] | ≥ 35% (US), ≥ 25% (LatAm) |
| Lesson completion | Started → completed | — | ≥ 80% |
| Step skip rate | Per step type | — | < 20% per type; investigate above |
| Grading appeal rate | Appeals / graded answers | — | < 3%; upheld appeals < 1% |
| AI cost per paying user | From the gateway | Model in [08](08-unit-economics.md) | ≤ $1.50/month typical; ≤ $3 at p95 |

### Validation metrics from David's planning brief (each one can flip a decision)

| Metric | Definition | Fail line / decision it drives |
|---|---|---|
| **Sustained intake at week 12, by cohort** | Median new items/day actually completed in week 12 | **Median < 10 → the market sits at 5**; flip the default intake ([04 D4.8](04-review-model.md#d48-intake-ladder--recommendation--needs-davids-check-adopted-from-davids-planning-brief-replaces-the-earlier-5-new-wordsday)) |
| **Week-3-to-8 drop-off vs intake** | Drop-off curves split by 5 / 10 / 15 per day | Anki experience predicts drop-off rises with intake. **Flat across 5/10/15 = a defensible claim no competitor can make**; a rising curve means tightening the review budget |
| **AI false-accept rate** | Native-speaker audit of AI-unflagged productions, **per learner native language** | **> 5% = fail.** The hard-constraint risk is live; raise the flag threshold or switch model ([05 §5.3](05-lessons-and-exercises.md#53-ai-grading-of-free-text)) |
| **Content error rate** | Native-speaker audit by content type | **> 2% = fail.** Pause that content type ([06 §6.8](06-content-pipeline.md#68-qa-and-safety-chain-for-generated-content)) |
| **Measured encounters per target word** | From the encounter log, first 30 days, by source | Replaces the brief's *assumed* "3 per story / 2 per video". If it lands well below the 8–12 target, the 10/day case weakens and must be re-modelled |
| **Provisional-promotion reversal rate** | Share of production-based promotions undone by the next keyed review | A high rate means learner self-ratings + AI non-flags are over-promoting |

**These are instrumented in Phase 1, before any growth spend** (brief: "instrument this early — it validates or invalidates the whole intake model").

### Learning-quality metrics
- **Recall calibration:** predicted vs actual recall on graded items (log loss / calibration error). It must stay at least as good as plain FSRS on the same data.
- **Deep resurfacing pass rate** ≥ 85% (otherwise tighten the deep criteria in [04 §4.4](04-review-model.md#d43-deep-criteria--recommendation--needs-davids-check)).
- **Lookup rate on "known" words**: lookups/1,000 tokens read on items the model thinks are known. Should fall over time.
- **Coverage growth**: Δ coverage % per 4 active weeks, by starting level.
- **Placement accuracy**: proportion of "probably known" words later looked up or failed. Target < 15%.
- **Audit-probe calibration**: about 1 random keyed item per user per day, from all states, compared with predicted P(known). This is the ground truth behind most of the checks above. The full test plans for every measurement (placement, cloze, flagger, recap honesty, FSRS, "Locked in", validator, interests, progress screens) are in [14](14-gaps-and-test-plans.md).

## 10.2 Risks

| # | Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|---|
| R1 | **Copyright** claims from simplifying or reusing news/articles | Medium–high if scraping; low with plan in [06](06-content-pipeline.md) | High (takedowns, store removal, damages) | Source tiers; no scraping; AI-original explainers with overlap checks; lawyer review before launch; takedown process |
| R2 | **YouTube terms** violation (paid embeds, overlays, caption scraping) | High if YouTube is put in the paid lesson | API key revoked, legal | No YouTube in MVP; free "Watch" extras only; creator partnerships |
| R3 | **AI cost blow-up** (companion heavy users, price rises such as Gemini 3.8 Flash doubling on 1 Jan 2027) | Medium | Margin | Gateway routing, fair-use caps, cost watchdog, provider independence |
| R4 | **AI quality**: wrong grading, unnatural stories, wrong glosses | Medium | Trust and retention | Gold-set evaluation in CI, confidence-based "partial", appeals, human-reviewed core glosses, naturalness sampling |
| R5 | **Cold start**: thin corpus for some interests and levels | High at launch | Weak recommendations | 1,500-text launch corpus; AI stories fill gaps on demand; matching falls back to neighbouring topics |
| R6 | **VOA archive disappears** | Medium | Loss of public-domain audio | Mirror the needed public-domain items with credits at ingest |
| R7 | **Review pile-up** churn | Medium | Retention | Backlog throttle, capped speed round, welcome-back queue |
| R8 | **Encounter farming** (scrolling to earn points) | Low–medium | Bad data | Dwell checks, same-day decay, deep requires graded retrievals |
| R9 | **Regulatory** (EU AI Act transparency, GDPR, minors) | Medium | Fines, store issues | AI labels, disclosure, data export/deletion, 13+ gate |
| R10 | **Store rejection** (Sign in with Apple, account deletion, paywall clarity) | Medium | Launch slip | Follow guidelines 4.8, 5.1.1(v), 3.1.2 (subscription disclosure) from the start |
| R11 | **Competition** (Duolingo Max, Speak, LingQ; **Clozemaster** is the closest functional competitor: frequency-ordered, real context, SRS, mobile, free tier, human-written Tatoeba sentences, 4.81★ from 2,036 US ratings per the brief) | Certain | Positioning | Lead with what Clozemaster lacks: interest-matched stories with 3× repetition, tracked per-word state, native-language glosses, listening training. Use human-written cloze sentences too ([11](11-distribution-and-positioning.md)) |
| R13 | **Free substitute:** learners already prompt ChatGPT to embed their words on a spaced schedule; tracked state is "a feature, not a moat" (brief) | High | Willingness to pay | The value is the *system*: placement, gap analysis, measured encounters, scheduling, no prompting. Show it in the Vault encounter timeline and in counts |
| R14 | **Distribution** (brief: 3/10, "the weakest result and the biggest threat") | High | Fatal if unsolved | Dedicated plan and budget in [11](11-distribution-and-positioning.md); distribution gates added to the roadmap below |
| R15 | **Hostility to AI content / AI chat** in the paying segment (brief: 1.6k-upvote threads) | Medium–high | Conversion, reviews | AI chat optional and labelled; production opt-out; human-written cloze; "Prefer real texts" setting; AI labels |
| R16 | **Time cost of 10/day** (≈ 30–35 min/day by my estimate) is higher than typical app sessions | Medium | Retention | Two ~15-min blocks; 5/day on-ramp and low-energy settings; week-12 intake metric decides the default |
| R17 | **AI false accepts** lengthening intervals on weak words | Medium | Silent learning harm | Hard constraint: AI may only flag; learner/answer-key authority; provisional promotions reversed by keyed review; false-accept audit ≤ 5% |
| R12 | **Native-language quality** (glosses/placement for Spanish/Portuguese) | Medium | Trust | Human-reviewed glosses; per-native-language calibration; native-speaker beta testers |

## 10.3 Phased roadmap (milestones and exit criteria; no calendar estimates)

### Phase 0 — Foundations
**Build:**
- Lexicon database: NGSL + NAWL/TSL/BSL + CEFR-J/Octanove tags + SUBTLEX ranks + PHRASE/PHaVE chunks + concreteness.
- Glosses for Spanish and Brazilian Portuguese with human review of the core ~3,500 items.
- Placement item bank: ~420 real-word items per native language with native-speaker-checked translated options ([03 §3.6](03-placement-test.md#36-item-bank-and-edge-cases)).
- Test harnesses: synthetic learners, the seeded-error validator suite, and gold sets for the flagger and interest parsing ([14 §14.2](14-gaps-and-test-plans.md#142-how-well-test-our-tests-the-approach)).
- Content ingest pipeline (tokenise → lemma/sense/chunk → difficulty → tags → QA).
- LLM gateway with prompt registry.
- Gold sets for grading.
- Supabase schema + PowerSync.

**Exit criteria:**
- Lexicon covers 4,000 lemmas + ~600 chunks with glosses in both native languages.
- Ingest produces coverage/CEFR/tags for 100 test texts.
- Grading judge reaches QWK ≥ 0.70 on the gold sets.

### Phase 1 — Core loop (internal alpha)
**Build:**
- Onboarding (O1–O7, O10).
- Placement stages 1–3 (harder-and-harder real words, confirmation items, phrase/sense probes), validated with synthetic learners before alpha.
- The MVP-priority gap fixes in [14 §14.1](14-gaps-and-test-plans.md#141-gaps-where-users-may-feel-lost-or-the-system-doesnt-connect): one headline word count, plain state labels, "why this word?", phrase-aware reader, audit probes.
- 5-item lesson blocks with triage, anchor (text-only allowed), story (3× repetition, ≤ 1.5% density), keyed cloze, one opt-out production step + recap card, time-capped speed round.
- Intake ladder (5 on-ramp → 10 default) and the ±7-day pool.
- This is the **smallest test of the core thesis** (frequency-ordered + context + production + SRS): no listening, no companion, no images yet.
- Chip reader.
- Encounter engine + FSRS hybrid.
- Vault.
- Today screen with all states.
- Offline next lesson.

**Exit criteria:**
- 10+ internal/friendly users complete 14 consecutive days of lesson blocks.
- No data-loss bugs in sync.
- Encounter replay is deterministic (same events → same item_state).
- **Hard-constraint test suite passes:** no code path lets an AI verdict alone set `promotion_applied = true`. This is enforced in the scheduler as a unit-tested invariant, not as a convention.
- **Instrumentation live:** measured encounters per target, story target counts, AI flag/accept logs, provisional-promotion reversals.

### Distribution groundwork (runs alongside Phases 0–3; brief: "solve distribution before writing more code")
See [11](11-distribution-and-positioning.md). **Gate before Phase 3 work starts:** a waitlist or free-tool audience of a size David sets, plus at least one channel with a measured cost per signup below the per-install ceiling in [08 §8.5](08-unit-economics.md#85-what-this-implies-for-growth-spend-my-estimate).

### Phase 2 — Full lesson mix + content (closed beta)
**Build:**
- Listening (AI mini-podcasts + VOA audio).
- Opinion prompt.
- Picture description with the scene library.
- Excerpts.
- Rotation rules.
- Interest feedback after every text, the interests page and "Because you asked for…" labels ([13](13-interests-and-feedback.md)).
- Read shelf + "articles with your words".
- AI story generator.
- Launch corpus (~1,500 texts).
- TTS caching.

**Exit criteria (50–200 beta users, Spanish/Portuguese native speakers):**
- Lesson completion ≥ 80%.
- Skip rate < 20% per step type.
- Appeal rate < 3%.
- AI cost ≤ $1.50/active user/month.

### Phase 3 — Monetisation and launch readiness
**Build:**
- Account (Apple/Google/email), anonymous-to-account merge.
- Paywall + 7-day trial + annual plan + regional pricing (RevenueCat).
- Restore purchases.
- Billing grace screens.
- Lite mode.
- Companion with fair-use limits.
- Notifications.
- Settings (all of P3).
- Data export/deletion.
- AI labels and disclosures.
- Licences/attributions screen.
- Analytics taxonomy.
- Store listings in English/Spanish/Portuguese.
- Legal review of the content pipeline.

**Exit criteria:**
- App Store + Play approval.
- Purchase flows tested, including sandbox renewals, cancellations and refunds.
- Legal sign-off on content sources and privacy policy.

### Phase 4 — Public launch and learn
**Run:**
- Launch in the Spanish/Portuguese native-language markets + English-only mode.
- Watch the §10.1 dashboards.
- A/B tests: trial length 7 vs 14 days; image vs text anchors; companion model choice.

**Exit criteria:**
- D30 retention and trial-to-paid at or above the targets in §10.1 for two consecutive cohorts.
- Then start Phase 5.

### Phase 5 — Expansion (v1.1+)
- Free "Watch" extras with partnered YouTube creators.
- Voice mode (STT) for the companion and opinion answers.
- Listening placement check.
- URL/paste importer.
- Vault export.
- Badges.
- Next 2–3 native languages (by waitlist).
- HLR-style model fit on NeuLang's own encounter data.
- Licensed podcasts and news.
- US web checkout.
- **Later:** Spanish and French as *target* languages (David decided). Each needs its own lexicon, chunk lists, placement bank and content corpus, so it's effectively Phases 0–2 again per language.

---

## Sources
- [S1] Phiture / Business of Apps retention benchmarks — https://www.businessofapps.com/data/app-retention-rates/
- [S2] Adapty, *State of In-App Subscriptions* — https://adapty.io/state-of-in-app-subscriptions/
- [S3] RevenueCat, *State of Subscription Apps 2026* — https://www.revenuecat.com/state-of-subscription-apps/
- David's planning brief (2026-10-13): validation metrics, competitor data, distribution score — [`internal/source/neulex-planning-brief.md`](../../internal/source/neulex-planning-brief.md)
