# 08 · Unit economics at $8/month

[← Back to plan index](../neulang-plan.md)

**David decided:** $8/month. Everything else here is **Recommendation — needs David's check**.
- All costs are **my estimates** from public list prices (Oct 2026) and the usage assumptions stated in each table.
- Re-run them with real telemetry after launch. The cost model should be a live dashboard (see [09](09-architecture.md)).

---

## 8.1 Revenue per subscriber

| Line | Monthly plan | Notes |
|---|---|---|
| List price | **$8.00** | David decided |
| Sales tax / VAT | US: added on top by the store. EU/UK/BR: **price includes VAT**, so $8 ≈ $6.50–6.70 before store fees at 20–23% VAT | Store prices are VAT-inclusive outside the US |
| Store fee | **15%** → $6.80 (US) | Apple Small Business Program: 15% if proceeds ≤ $1M/year, from the first billing cycle (otherwise 30% in a subscriber's first year, then 15%) [S1]. Google Play: 15% on subscriptions [S2] |
| RevenueCat | 1% of tracked revenue once above $2,500/month [S3] → −$0.08 | Free below that |
| **Net (US)** | **≈ $6.72** | |
| **Net (EU, 20% VAT)** | **≈ $5.60** | |

### D8.1 Annual, lifetime and regional pricing — **Recommendation — needs David's check**

**$8/month stays** (David decided). David's planning brief adds evidence for more options:
- one-time purchases beat subscriptions in this segment ("$25 up front is a million times better than a $4 monthly subscription");
- the brief recommends **$6–8/month billed annually, or lifetime $50–199, with one-click cancellation**.

**Recommendation.**
- **Annual plan at $71.99/year** (= $6/month, 25% off monthly). This sits inside the brief's $6–8 annual range and below Speak's $83.99/year.
  - Annual plans dominate in education: 59–66% of subscribers.
  - Annual plans bring roughly 2× the revenue per install of monthly-only [S4].
  - Competitors anchor the price well above this: Speak $83.99/year, LingQ $119.99/year, Migaku $144/year [S5]. Their monthly prices are $17.99, $14.99 and $15, so **$8/month is already a value position**.
  - This replaces my earlier $59.99 suggestion, which undercut the brief's range with no evidence that the cheaper price converts better.
- **Lifetime at $149, as a limited "founding member" offer** (capped number of seats, e.g. the first 1,000 buyers), with the AI fair-use policy applying.
  - My estimate: $149 nets ≈ $127 after 15% fees.
  - That covers ≈ 75 months of a typical user's cost (≈ $1.69/month, §8.2), but only ≈ 27 months of a heavy user's.
  - So lifetime is safe only with companion fair use and a seat cap.
  - It also brings in cash up front, which helps fund distribution (the weakest area; see [11](11-distribution-and-positioning.md)).
- **One-click cancellation:** a "Manage / cancel subscription" deep link in Settings (P3) that opens the store's subscription page directly, and the same on the web. It's a trust signal the brief's evidence says this segment cares about.
- **Regional pricing** (purchasing-power based) for the launch native-language markets: e.g. Brazil and Mexico at about 50–60% of the US price. Trial-to-paid varies by region (North America 34.2% vs Middle East & Africa 19.5% [S4]), and unadjusted prices kill conversion in Latin America.

## 8.2 Cost per active user per month

### Usage assumptions (typical paying user, updated for the 10/day default)
- 25 active days/month × **2 lesson blocks** = **50 blocks/month** (10 new items/day; see [05 D5.1](05-lessons-and-exercises.md#d51-lesson-block-fixed-slots-rotating-content--recommendation--needs-davids-check)).
- Lesson stories ≈ 1,000 words at B1 (density rule: 5 targets × 3 appearances ≤ 1.5%).
- 10 free-reading AI stories/month.
- 200 companion messages/month.
- Half the lesson stories played with audio; 8 listening-training podcasts/month.

### LLM

**Per lesson block** (≈1,000-word personal story with ~1.5× validator retries + use-it prompt + AI flag check + chat replies + sense tagging; cloze mostly from Tatoeba, so ~free): ≈ **13k input + 3.8k output tokens**. ×50 blocks = **650k in / 190k out per month**.

| Model (list price per 1M in/out) | Lesson blocks / month | Notes |
|---|---|---|
| Gemini 2.5 Flash-Lite ($0.10/$0.40) [S6] | $0.14 | Oldest; quality check needed |
| Claude Haiku 5.5 ($0.10/$0.50, ≤100k prompts) [S7] | **$0.16** | Cheapest of the current models |
| gpt-5-nano ($0.05/$0.40) [S8] | $0.11 | Very cheap; check story quality |
| Gemini 3.1 Flash-Lite ($0.25/$1.50) [S6] | **$0.45** | Planning baseline |
| gpt-5-mini ($0.25/$2.00) [S8] | $0.54 | |
| Gemini 3.8 Flash ($0.75/$3.75; **$1.50/$7.50 from 1 Jan 2027**) [S6] | $1.20 → $2.40 | Stronger; use only for flag checks / judging samples |
| Claude Sonnet 5.5 ($2/$10) [S7] | $3.20 | Gold-set grading and audits only |

- **Batch discount:** pre-generating tomorrow's stories overnight through batch APIs gets ~50% off [S6][S8]. Stories are ~40% of tokens.
- **Prompt caching:** cached reads cost ~10% for Claude [S7]. Static system prompts and few-shot examples are cached.
- **Shared-story lever:** when two users at the same level share ≥ 4 of a block's 5 targets within their ±7-day pools, one story serves both. A topic-coherent pool makes such overlaps common. This is the main COGS lever as content volume grows (the brief calls content generation "the scaling cost").
- **Realistic lesson LLM cost on the baseline model: ≈ $0.35/month at 10/day** (≈ $0.18 at 5/day).

**Free reading AI stories:** 10 × (2k in + 2k out) ≈ 20k/20k → **≈ $0.04** on the baseline model.

**Companion chat**, assuming ~4k input (recent history + persona + user's words) + ~300 output per message:

| Usage | Gemini 3.1 Flash-Lite | Claude Haiku 5.5 | Gemini 3.8 Flash (2027 price) |
|---|---|---|---|
| Per message | $0.0015 | $0.00055 | $0.0083 |
| Typical: 200 msgs/month | **$0.29** | $0.11 | $1.66 |
| Heavy: 3,000 msgs/month (100/day) | $4.35 | $1.65 | $24.90 |

### TTS (text-to-speech)

**Price options:**
- Google WaveNet/Standard $4/1M characters (4M free per month);
- Neural2 $16/1M;
- Chirp 3 HD $30/1M (1M free);
- Studio $160/1M [S9];
- OpenAI tts-1 $15/1M, tts-1-hd $30/1M, gpt-4o-mini-tts ≈ $0.015/minute [S8];
- ElevenLabs Flash ≈ $50/1M, Multilingual ≈ $100/1M [S10];
- Gemini 2.5 Flash TTS: $0.50/1M text tokens + $10/1M audio tokens (≈ $0.015/minute) [S6].

| Audio | Volume | Cost |
|---|---|---|
| **Shared, cached** (word audio for ~4,600 items × example sentences, 1,500 corpus texts, ~500 shared mini-podcasts) | ≈ 5M characters, one-off | Chirp 3 HD ≈ **$150**; WaveNet ≈ $20; ElevenLabs Multilingual ≈ $500 |
| **Personal stories** (generated only when the user taps ▶) | 25 stories × ~6,000 chars (≈ 1,000 words, ≈ 7 min each) | WaveNet ≈ **$0.60** · gpt-4o-mini-tts ≈ $2.60 · Chirp ≈ $4.50 |
| **Listening-training podcasts** | 8 × ~90 s | gpt-4o-mini-tts / Gemini Flash TTS ≈ $0.18 · WaveNet ≈ $0.04 |
| Long-tail word taps in free reading | — | **On-device TTS** (`flutter_tts`): free, offline |

**Recommendation:**
- Chirp 3 HD (or similar quality) for shared, cached audio.
- **WaveNet-class voices for personal stories.** At 1,000-word stories, premium per-minute voices would cost $2.60–4.50/month per user, which is too much.
- A more expressive voice only for the short listening-training podcasts, where voice quality matters most.
- On-device TTS for long-tail taps.
- Typical cost: **≈ $0.80/month** at 10/day.

### STT (speech-to-text, v1.1 voice mode)
gpt-4o-mini-transcribe $0.003/minute [S8]; AssemblyAI ≈ $0.0025/minute; Deepgram Nova-3 ≈ $0.0048/minute. 30 minutes/month ≈ **$0.09**. Negligible.

### Images
One-off ≈ **$100** for the whole MVP library (see [07](07-images.md)). No per-user cost.

### Infrastructure
- Supabase Pro: $25/month base, including 100k MAU, 8 GB database, 250 GB egress and 100 GB storage [S11].
- PowerSync: free tier up to 2 GB synced/month, then paid [S12].
- Object storage + CDN for audio (Cloudflare R2 has no egress fees).
- Error tracking and analytics on free/low tiers.
- At small scale, fixed costs dominate: ≈ $100–200/month total, so **≈ $0.10–0.30 per active user** at 500–2,000 users, falling with scale.

### Total

| | Typical paying user (10/day) | Heavy user (15/day, all audio played, companion 100/day) |
|---|---|---|
| Lesson LLM | $0.36 | $0.54 |
| Reading AI stories | $0.04 | $0.15 |
| Companion | $0.29 | $4.35 (Flash-Lite) / $1.65 (Haiku 5.5) |
| TTS | $0.80 | $2.10 |
| Infra | $0.20 | $0.30 |
| **Total cost** | **≈ $1.69** | **≈ $4.74–7.44** |
| Net revenue (US monthly) | $6.72 | $6.72 |
| **Contribution** | **≈ $5.03 (63% of list)** | ≈ −$0.72 to +$1.98 |

**Conclusion.**
- $8/month works for typical users even at the 10/day default: content generation + story audio ≈ $1.15/month, the largest variable cost line, as the brief predicted.
- **What can break margins:**
  - heavy companion use on anything pricier than a Haiku-5.5-class model. So route the companion to the cheapest passing model by default, not just after the soft limit;
  - premium TTS voices on long personal stories;
  - annual or lifetime buyers in VAT markets who are also heavy users.

## 8.3 The "unlimited" AI companion

### D8.2 Fair-use design — **Recommendation — needs David's check**
0. **Optional and labelled** (brief): the companion bubble can be hidden in settings, and its first screen says it's an AI. It's never needed to finish a lesson.
1. **Model routing:** the companion runs on the cheapest model that passes the quality evaluation. Run the same gold-set approach as grading ([05 §5.3](05-lessons-and-exercises.md#53-ai-grading-of-free-text)) over Haiku 5.5, gpt-5-nano and 3.1 Flash-Lite.
2. **Context control:** keep only the last ~10 turns plus a running summary, which keeps input around 2–4k tokens. Cache the persona and system prompt.
3. **Soft limit at ~100 messages/day:** after that, replies go to the cheapest model and the conversation summary resets. No visible block.
4. **Hard abuse cap ~300 messages/day:** show sheet C3 ("Let's continue tomorrow"). Very few users will ever see it.
5. **Cost watchdog:** flag users whose AI cost exceeds **$3/month**. Look at whether they're power learners (good) or scripts (block).
6. **Marketing wording:** avoid a bare "unlimited". Use "Chat as much as you like — fair use applies" with the policy linked. Advertising "unlimited" with hidden caps is a consumer-protection risk.
7. The **EU AI Act Art. 50** chatbot disclosure ("You're chatting with an AI") appears on first open of C1 [S13].

## 8.4 Trial vs freemium

### D8.3 Monetisation model — **Recommendation — needs David's check**

**Evidence.**
- RevenueCat 2026 [S4]:
  - hard-paywall apps convert **10.7%** of downloads to paid by day 35, vs **2.1%** for freemium;
  - one-year retention of payers is about the same (27% vs 28%).
- Trial-to-paid by trial length [S4]:
  - ≤ 4 days: 25.5%;
  - **5–9 days: 37.4%**;
  - 17–32 days: 42.5%.
- Adapty [S14]:
  - global install-to-trial ~10.9%, trial-to-paid ~25.6%;
  - in education, **23.5% of trial starts happen on day 31 or later**, so the paywall must keep reappearing for non-converters.
- Free AI usage costs money, so a generous freemium tier is expensive.

**Options.**
- **A. Hard paywall after onboarding**, with a free trial.
- **B. Freemium** (free tier with limits + subscription).
- **C. Hybrid (recommended):**
  - placement + first lesson free;
  - then a paywall with a **7-day free trial**;
  - users who decline keep a **minimal free mode**: their Vault, reviews of words they already have, and up to 1 corpus article a day with no AI;
  - the paywall reappears on high-intent moments (new lesson, companion, AI story);
  - the paywall offers monthly $8, annual $71.99 and (while seats last) lifetime $149 (D8.1). Copy uses counts ("3,650 words this year"), never a coverage % over one month (see [02 D2.5](02-english-lexicon-and-languages.md)).

**Recommendation: C.**
- It behaves almost like a hard paywall: no free new lessons, no free AI. But it doesn't throw away the placement and first-lesson investment, which helps the day-31+ late conversions.
- Run an A/B test of a 7-day vs 14-day trial once there's traffic (the evidence shows a mild gain for longer trials).
- Send a trial-ending reminder 2 days before the charge (see [01](01-scope-and-journey.md)).

### D8.4 Web checkout (US) — **Recommendation — needs David's check** (later)
- After *Epic v. Apple*, US apps may currently link to web checkout with **0% Apple commission**. Apple has proposed a 5–15% fee, the court is to decide the rate, and the Supreme Court may rule around mid-2027 [S15].
- Web payments still cost ~3% + $0.30 (≈ 6.6% on $8). That's a net saving of ~8 points vs 15%.
- **Not for MVP** (extra work: Stripe, entitlement sync, RevenueCat Web). Revisit when US revenue matters and the rules are stable.

## 8.5 What this implies for growth spend (my estimate)
- Funnel assumption: 4–10% of installs become paid (hybrid paywall), average ~6 months paid lifetime, ~$5.03 contribution a month. That gives **≈ $1.20–3.00 contribution per install**, which is the **ceiling for paid acquisition cost per install** before overheads.
- Education apps retain poorly (D30 ≈ 2–3% overall [S16]). David's planning brief scored distribution **3/10, the weakest result and the biggest threat**. So **organic / creator-driven growth** is likely to matter more than paid ads. The full distribution plan and budget are in [11](11-distribution-and-positioning.md).

---

## Sources
- [S1] Apple App Store Small Business Program — https://developer.apple.com/app-store/small-business-program/
- [S2] Google Play service fees — https://support.google.com/googleplay/android-developer/answer/112622
- [S3] RevenueCat pricing — https://www.revenuecat.com/pricing/
- [S4] RevenueCat, *State of Subscription Apps 2026* — https://www.revenuecat.com/state-of-subscription-apps/
- [S5] Competitor pricing pages: LingQ (https://www.lingq.com/en/pricing/), Migaku (https://migaku.com/pricing), Speak (https://www.speak.com/), Duolingo Max (https://www.duolingo.com/max)
- [S6] Gemini API pricing (incl. Batch, TTS) — https://ai.google.dev/gemini-api/docs/pricing
- [S7] Anthropic Claude pricing — https://www.anthropic.com/pricing
- [S8] OpenAI API pricing — https://openai.com/api/pricing/
- [S9] Google Cloud Text-to-Speech pricing — https://cloud.google.com/text-to-speech/pricing
- [S10] ElevenLabs API pricing — https://elevenlabs.io/pricing/api
- [S11] Supabase pricing — https://supabase.com/pricing
- [S12] PowerSync pricing — https://www.powersync.com/pricing
- [S13] EU AI Act Art. 50 — https://ai-act-service-desk.ec.europa.eu/en/ai-act/article-50
- [S14] Adapty, subscription benchmarks (education) — https://adapty.io/state-of-in-app-subscriptions/
- [S15] *Epic Games v. Apple*, contempt order (N.D. Cal., Apr 2025) and subsequent proceedings on commission for link-outs
- [S16] Phiture / Business of Apps, retention benchmarks — https://www.businessofapps.com/data/app-retention-rates/
