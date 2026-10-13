# 09 · Flutter technical architecture

[← Back to plan index](../neulang-plan.md)

**David decided:** Flutter. Everything else here is **Recommendation — needs David's check**.

---

## 9.1 System overview

```
┌──────────────── Flutter app (iOS + Android) ────────────────┐
│ UI (Riverpod state, go_router)                              │
│ Local DB: SQLite (via PowerSync SDK) ── encounter events,   │
│   item_state, downloaded lessons, texts, glosses            │
│ Scheduler (Dart `fsrs` + encounter rules) runs ON DEVICE    │
│ Chip reader tokens/lemmas precomputed server-side           │
│ Audio: just_audio (cached files) · flutter_tts (fallback)   │
│ Purchases: RevenueCat SDK · Analytics: PostHog SDK          │
└───────────────┬──────────────────────────┬──────────────────┘
                │ sync (PowerSync)         │ HTTPS (auth JWT)
┌───────────────▼──────────────┐  ┌────────▼───────────────────────────┐
│ Supabase Postgres + Auth     │  │ Edge Functions / small API service │
│  users, item_state, events,  │  │  LLM gateway (routing, caching,    │
│  lessons, texts, glosses     │  │  quotas, moderation, logging)      │
│  Row-Level Security          │  │  grading · companion · generation  │
└───────────────┬──────────────┘  └────────┬───────────────────────────┘
                │                          │
┌───────────────▼──────────────────────────▼───────────────────────────┐
│ Batch workers (nightly): lesson pre-generation (Batch APIs),         │
│ content ingest (tokenise → lemma/sense → difficulty → tags → QA),    │
│ TTS rendering, item-state rebuilds, model re-fits                    │
│ Storage: Supabase Storage or Cloudflare R2 (audio, images) + CDN     │
└──────────────────────────────────────────────────────────────────────┘
```

### D9.1 Backend — **Recommendation — needs David's check**

| Option | Pros | Cons |
|---|---|---|
| **Supabase** (Postgres + Auth + Storage + Edge Functions) | Relational data fits this domain (users × items × events); Row-Level Security; SQL analytics; Pro plan $25/month with 100k MAU included [S1]; a mature Flutter SDK | Edge Functions are Deno/TypeScript, a second language next to Dart |
| **Firebase** (Firestore + Functions) | Very common with Flutter; offline cache built in | Document model is awkward for item × user × event queries and analytics; costs grow per read/write with event-heavy usage |
| **Custom** (e.g. Dart Frog / Node + Postgres) | Full control | More ops work |

**Recommendation: Supabase + PowerSync.**
- PowerSync syncs Postgres to on-device SQLite with offline writes and conflict handling, and has a Flutter SDK [S2].
- The **encounter log is append-only**, which makes sync conflicts nearly impossible: events have client UUIDs and are idempotent.
- `item_state` is recomputed deterministically from events: on the device for speed, re-verified on the server.
- **Promotion invariant (David's hard constraint):**
  - the scheduler is the only code that writes `promotion_applied`;
  - it accepts a promotion only from events with `authority ∈ {answer_key, learner}`;
  - the LLM gateway's responses can set `ai_flagged` / `ai_verdict` but **cannot reach the promotion path**.

  It's a unit-tested invariant on both client and server, and a CI check fails if any AI-sourced field feeds an FSRS rating ([04 §4.0](04-review-model.md#40-promotion-authority-the-rule-every-other-part-obeys)).

## 9.2 Offline behaviour

| Feature | Offline? | How |
|---|---|---|
| Placement test | ✅ | Item bank for the user's native language bundled with the app (~420 items with checked translated options, [03 §3.6](03-placement-test.md#36-item-bank-and-edge-cases)) |
| Next lesson | ✅ | Pre-generated and downloaded nightly (text + images + audio) |
| Speed round / reviews | ✅ | Local scheduler |
| Reading downloaded texts | ✅ | Glosses for the text bundled with it |
| Free-text grading | ⚠️ | Local pre-check gives provisional credit; LLM re-grades when back online and may adjust |
| Companion, AI story generator | ❌ | Show E1 with "available when online" |
| Purchases | ⚠️ | Cached entitlement, valid for a grace period |

**Download budget:**
- next lesson ≈ 1–3 MB with audio;
- keep the 2 next lessons plus 10 recommended texts ≈ 15–30 MB;
- "Wi-Fi only for audio" setting.

## 9.3 Data model (core tables)

All tables carry `created_at`/`updated_at`. User-owned rows have RLS so that `user_id = auth.uid()`.

```text
users(id, created_at, native_lang, gloss_mode[native|english], goal,
      intake_setting[onramp5|default10|low5|ceiling15], intake_unlocked_15, production_enabled,
      ai_chat_enabled, companion_visible, prefer_real_texts, review_budget_sec_per_block, desired_retention, timezone, day_cutoff_hour=4,
      cefr_vocab_estimate, size_estimate, size_ci, placement_version, age_confirmed_13plus)
-- interests: interest_topics, user_interests, user_custom_interests, text_feedback,
--            content_requests, text_topics — see 13 §13.10
-- lexicon:   lexicon_releases, corpora, items, item_forms, phrase_patterns, item_components,
--            item_topic_freq, item_tags, item_l1, item_relations, item_live_stats,
--            lexicon_candidates — see 12 §12.6 (supersedes the simpler items row drafted here)
items(...)                  -- see 12 §12.6; plus concreteness, family_id, ngsl_rank used by 05/07
cloze_sentences(id, item_id, sense_id, text, blank_span, answer_forms[], source[tatoeba|corpus|ai],
                source_id, author, licence, translation_lang, translation_text, audited_at, status)
senses(id, item_id, definition_en, example_en, cefr)
glosses(sense_id, lang, text, status[auto|reviewed], reviewer_id)
false_friends(item_id, lang, note)
images(id, kind[anchor|scene], url, source[ai|unsplash|pexels], attribution, alt_text,
       item_ids[], style_version, status)
texts(id, kind[ai_story|ai_explainer|voa|gutenberg|cc_adapted|as_is], title, body,
      source_url, author, licence, changes_note, ai_generated, level_cefr, topic_ids[],
      embedding, token_count, lexical_profile_json, audio_url, status, created_at)
text_tokens(text_id, position, surface, item_id, sense_id)        -- precomputed tokenisation
lessons(id, user_id, local_day, plan_json, status[planned|ready|in_progress|done],
        current_step, generated_with_model, created_at)
lesson_steps(lesson_id, idx, type, payload_json, completed_at, skipped)
encounter_event(...)        -- see 04 §4.7
item_state(...)             -- see 04 §4.7
daily_item_rollup(...)      -- see 04 §4.7
placement_responses(user_id, item_id, stage[ladder|confirm|probe], format[l1_mcq|reverse_mcq|en_definition],
                    answer[option_idx|idk], correct, latency_ms, lang, ability_after, se_after)
audit_probes(user_id, item_id, state_at_probe, predicted_p, correct, local_day)   -- ground truth, 14 §14.2
chat_sessions(id, user_id, kind[textercise|companion], lesson_id, started_at)
chat_messages(id, session_id, role, content, model, tokens_in, tokens_out, cost_usd,
              moderation_flags, created_at)                       -- retention policy below
grades(id, user_id, step_id|message_id, item_id, ai_verdict, ai_confidence, ai_flagged, model,
       prompt_version, learner_rating, appealed)       -- advisory only; scheduling reads learner_rating
audits(id, kind[production_false_accept|content_error], target_type, target_id, auditor_id,
       learner_native_lang, label, note, created_at)  -- feeds the ≤5% / ≤2% fail lines
content_reports(id, user_id, target_type, target_id, reason, note, status)
ai_usage_daily(user_id, day, tokens_in, tokens_out, cost_usd, messages)   -- quotas & cost watchdog
entitlements(user_id, product_id, status, expires_at, source[apple|google|web])  -- mirrored from RevenueCat webhooks
```

## 9.4 LLM gateway

One server-side service that every AI call goes through. The app **never** holds provider keys.

- **Routing:** per task (story_gen, grade, chat, companion, explainer, gloss, sense_tag, classify — parsing "what I want to read" text into topics, [13 §13.5](13-interests-and-feedback.md#135-turning-the-text-box-into-something-the-system-can-use)), with a primary model, a fallback model on errors, and a model for appeals.
- **Prompt registry:** versioned prompts (`prompt_version` stored on every output) so grading regressions can be traced.
- **Quotas:** per-user daily message and cost counters (`ai_usage_daily`) enforce the fair-use rules in [08 §8.3](08-unit-economics.md#83-the-unlimited-ai-companion).
- **Moderation** on input and output; refusal templates.
- **Caching:** provider prompt caching, plus our own cache for deterministic tasks (glosses, sense tags, shared stories).
- **Observability:** latency, error rate, tokens and cost per task and per model. A daily cost dashboard is the live version of [08](08-unit-economics.md).
- **Provider independence:** the prices and models in this plan will change. The gateway lets you switch models with a config change, after re-running the gold-set evaluations.

## 9.5 Key Flutter packages (indicative)
- State/navigation: `flutter_riverpod`, `go_router`.
- Local data/sync: `powersync` (SQLite) [S2].
- Scheduling: `fsrs` [S3].
- Audio: `just_audio`, `audio_session`; `flutter_tts` for on-device fallback.
- Purchases: `purchases_flutter` (RevenueCat).
- Notifications: `firebase_messaging` (push) + `flutter_local_notifications` (local reminders that work offline).
- Analytics/crash: `posthog_flutter`, `sentry_flutter`.
- Video (v1.1): `youtube_player_iframe` (official IFrame embed, which keeps NeuLang inside YouTube's rules) [S4].
- Text: a server-side lemmatiser (spaCy-class) precomputes tokens. On the device, only a small lemma lookup table is needed for the user's typed answers (pre-check).

## 9.6 Analytics and telemetry

**Event taxonomy** (screen IDs from [01 §1.4](01-scope-and-journey.md#14-screens-inventory)), product events only, never message content:
- Onboarding: `onboarding_step_viewed{step}`, `placement_started`, `placement_item_answered{stage,format,latency}`, `placement_completed{size_est,ci,items,duration}`.
- Monetisation: `paywall_viewed{placement}`, `trial_started{product}`, `purchase_completed`, `trial_converted`, `subscription_cancelled{reason?}`.
- Lessons: `lesson_started{type_mix}`, `lesson_step_completed{step_type,duration}`, `lesson_step_skipped`, `lesson_completed{duration}`, `lesson_abandoned{last_step}`.
- Reading: `text_opened{kind,coverage}`, `text_finished{read_through}`, `word_lookup{item_state}`.
- AI: `companion_message_sent{count_today}`, `grade_appealed`, `fair_use_soft_limit_hit`, `ai_flag_raised{exercise,lang}`, `production_recap_rated{rating,flagged}`.
- Intake and load: `intake_level_changed{from,to,reason[auto_escalate|user|throttle]}`, `block_completed{idx,minutes}`, `review_budget_exhausted{left_due}`, `cloze_answered{format,correct}`, `provisional_promotion_reversed`.
- Settings: `production_opt_out_toggled`, `ai_chat_toggled`, `prefer_real_texts_toggled`.
- Interests and feedback: `text_feedback_shown{text_kind,compact}`, `text_feedback_given{rating,n_reasons,has_text}`, `interest_bubble_tapped{topic,surface}`, `content_request_served{latency_days}`, `because_label_seen`, `interests_page_opened` ([13 §13.10](13-interests-and-feedback.md#1310-data-model)).
- Understanding: `why_this_word_opened`, `audit_probe_answered{state,correct}`, `phrase_tapped{type}`, `chat_about_story_started`.
- Product health: `notification_opened{type}`, `offline_lesson_used`, `generation_failed{task}`.

**Learning-quality telemetry** (from the encounter log, not product analytics):
- recall rate by state and by days since last encounter;
- FSRS calibration;
- deep-resurfacing pass rate;
- coverage growth per active week.

## 9.7 Privacy, safety and compliance

### D9.2 Privacy baseline — **Recommendation — needs David's check**
- **Age:** require users to be **13+** (16+ in some EU countries without parental consent under GDPR Art. 8). Use a neutral age gate in onboarding. Don't build for children at launch: COPPA and kids-category rules would add a lot of work.
- **GDPR / LGPD (Brazil) / CCPA basics:**
  - privacy policy and terms;
  - lawful basis = contract (the service) + consent for optional analytics;
  - **data export** (P3: JSON of words, encounters, lessons);
  - **account deletion** in-app (also required by Apple [S5]), which deletes or anonymises all rows within 30 days;
  - EU hosting region for EU users, if Supabase's region choice allows.
- **Chat content:**
  - stored for 30–90 days for grading appeals and quality review, then deleted or aggregated;
  - "Delete chat history" in settings;
  - never used to train third-party models: use provider tiers that don't train on API data (e.g. Gemini paid tier [S6]; OpenAI/Anthropic API defaults).
- **AI transparency (EU AI Act Art. 50, applies from 2 Aug 2026):**
  - disclose the companion and Textercise partners as AI;
  - label AI-generated texts and images;
  - mark AI news explainers as AI-generated unless they're human-reviewed [S7].
- **Store privacy labels** (Apple privacy nutrition label, Google Data safety form) must match the analytics SDKs.
- **Moderation and safety:**
  - self-harm/crisis messages in the companion trigger a fixed safe response with resources;
  - report button everywhere (E6).

## 9.8 Engineering effort notes (technical, not calendar)
- **Most complex components:**
  1. The encounter engine + scheduler (deterministic replay, same-day decay, sync).
  2. The content ingest pipeline (tokenise → lemma/sense/chunk → difficulty → tags → QA).
  3. The LLM gateway with grading evaluations.
- **Main dependencies:**
  - the lexicon database (lists + glosses per native language) must exist before lessons can be generated;
  - the placement item bank (real words with native-speaker-checked translated options per native language) must exist before placement;
  - the synthetic-learner harness and gold-set evals ([14](14-gaps-and-test-plans.md)) must exist before the placement engine, flagger and story validator ship;
  - the RevenueCat + store product setup must exist before the paywall.
- **Risky integrations:**
  - store subscription edge cases (grace periods, Ask to Buy, refunds) — use RevenueCat to absorb them;
  - background audio on iOS;
  - push notification permission flows.

---

## Sources
- [S1] Supabase pricing — https://supabase.com/pricing
- [S2] PowerSync Flutter SDK — https://docs.powersync.com/client-sdk-references/flutter
- [S3] `fsrs` Dart package — https://pub.dev/packages/fsrs
- [S4] `youtube_player_iframe` — https://pub.dev/packages/youtube_player_iframe
- [S5] Apple App Review Guideline 5.1.1(v) (account deletion) — https://developer.apple.com/app-store/review/guidelines/
- [S6] Gemini API terms (paid tier data use) — https://ai.google.dev/gemini-api/terms
- [S7] EU AI Act Art. 50 — https://ai-act-service-desk.ec.europa.eu/en/ai-act/article-50
