# 06 · Content pipeline: sourcing, legal risk, difficulty, matching, AI generation

[← Back to plan index](../neulang-plan.md)

**David decided:**
- Content is a mix of (a) **real stories and news simplified by AI** for lower levels, (b) **real texts used as-is**, and (c) **stories written purely by AI**.
- Collect a large corpus and match it to each user's **interests and level**.
- Make above-level content accessible, so reading stays relevant and isn't only fable-style stories.

The legal and policy analysis below changes **how** (a) can be done, not whether. This is the highest-risk area of the plan. Everything here is **Recommendation — needs David's check**, and the legal items need **a lawyer's review before launch** (I'm not a lawyer; this is a research summary).

---

## 6.1 The core legal problem with "simplify real news"

- **Simplifying an article creates a derivative work**, which needs the rightsholder's permission. Fair use (US) is weakest when the use is commercial and substitutes for the original:
  - In *Thomson Reuters v. Ross*, the court found that copying for a competing commercial product was **not fair use**. The 3rd Circuit affirmed on 30 Sep 2026 [S1].
  - Courts have also noted that news is especially exposed to market-substitution arguments [S2].
- **Publishers are actively suing** over AI reuse of their articles:
  - *NYT v. OpenAI / Microsoft*: claims survived motions to dismiss [S3].
  - *USA Today / Gannett v. OpenAI*: filed 8 Oct 2026 [S4].
- **EU:**
  - The press publishers' right (DSM Directive Art. 15) covers online use of press publications beyond very short extracts.
  - Text-and-data mining is allowed only where rightsholders haven't opted out (Art. 4) [S5]. Most large publishers opt out.
- **AI-only text isn't copyrightable** in the US (Copyright Office, Part 2, Jan 2025) [S6]. NeuLang's purely AI stories can be copied by competitors. That's acceptable; just don't count them as a defensible asset.

**Conclusion.** Scraping commercial news and feeding it to an LLM to simplify is the **riskiest** possible way to deliver David's idea. There are safer ways to get the same user value: current, relevant, real-world topics at the learner's level.

## 6.2 Source tiers

### D6.1 Which sources to use — **Recommendation — needs David's check**

| Tier | Source | Licence | Can simplify? | Notes |
|---|---|---|---|---|
| **Safe** | **AI-original stories** | Ours (not copyrightable) | n/a | Bulk of lesson inputs |
| Safe | **AI-original "news explainers"** written from *facts* in several sources (facts aren't copyrightable), with source links | Ours | n/a | Replaces "simplified news"; see §6.3 |
| Safe | **VOA Learning English** (archive) | **Public domain**, credit required; excludes AP/Reuters/AFP items [S7] | Yes | Already level-graded, with audio. **Risk:** VOA was gutted in 2025 and no longer publishes new English content; archive pages may disappear [S8]. **Mirror the needed items** (allowed for public-domain works) |
| Safe | **Project Gutenberg / Standard Ebooks** | Public domain (US) | Yes | Classic fiction for B2+ and simplified for lower levels; remove Gutenberg's trademark header if modified |
| Safe | **Wikinews** | CC BY 2.5 | Yes, with credit and change note | Small, but real news |
| Safe | **Wikipedia** | CC BY-SA 4.0 | Yes, but **share-alike** applies to the simplified version | Good for topic explainers; publish attribution and licence on R4 |
| Safe | **Global Voices** | CC BY [S9] | Yes, with credit and change note | Real international stories |
| Safe | **US federal government works** (NASA, NOAA, NIH, etc.) | Public domain (17 U.S.C. §105) | Yes | Science and nature topics |
| **As-is only** | **The Conversation** | CC BY-ND: **no edits, no cuts, no translation** without author approval; republishing guidelines restrict AI use [S10] | **No** | Full text as-is with attribution only. Good for B2+ "real text" excerpts with the reader's gloss support. Partial excerpts count as cuts unless they follow the publisher's guidelines; check with them |
| **License later** | News agencies (AP, Reuters), graded-reader publishers, podcast networks | Commercial licence | Per contract | Once revenue justifies |
| **Avoid** | Scraping commercial news sites / paywalled content; YouTube audio/captions | — | — | Copyright + ToS + DMCA §1202 risk |

**Licence note on CC BY / BY-SA adaptations:** keep the title, author, source, licence link and a "changes made: simplified for learners by NeuLang (AI-assisted)" line [S11].

### D6.2 "Above-level content made accessible" — **Recommendation — needs David's check**
David's goal can be met without simplifying anything copyrighted:
1. **As-is plus reader support:** show the original with pre-glossed hard words, a native-language one-line summary per paragraph, and audio. Works for any licensed or public-domain text.
2. **Simplify only where the licence allows** (public domain, CC BY, CC BY-SA).
3. **AI-original explainer** on the same topic, written at the user's level, linking out to the original article. The link-out is good for publishers and keeps NeuLang on the facts-not-expression side.

## 6.3 AI-original news explainers (replacing "simplified news")

- **Input:** a topic and 3+ source URLs (from RSS headlines or Google News topics; only headlines and facts are used, not article text). The model is instructed to write from **facts only**, in its own words.
- **Safeguards:**
  - **n-gram overlap check:** < 5% of 8-word sequences may match any single source. Above that → regenerate.
  - No quotes longer than one short sentence, and only with attribution.
  - **Factuality check:** a second LLM pass lists each claim and verifies it against the sources. Unsupported claims are removed.
  - An "**AI-written explainer**" label with source links on R4.
- **EU AI Act Art. 50** (applies from 2 Aug 2026): AI-generated text published to inform the public on matters of public interest must be disclosed as AI-generated, *unless* it went through human review / editorial control with someone taking editorial responsibility [S12]. So: label every AI news explainer, or have a human editor sign off. Labelling is cheaper and is the recommendation.
- **Freshness:** about 10–20 explainers a week across the top topics at 3 levels. That makes the "news" feel alive without a newsroom.

## 6.4 Pure AI stories

- **Generation spec:**
  - inputs: target CEFR level, topic/genre, the block's new targets (3 at A1–A2, 5 at B1+; chosen as a **topic-coherent subset of the ±7-day pool**, [04 §4.8](04-review-model.md#48-load-control-and-intake-avoiding-review-pile-ups)), 2–3 review words;
  - constraints:
    - each new target **≥ 3×** in one coherent text;
    - new-target density **≤ 1.5%** of tokens, so length ≈ 200 words × new targets (≈ 1,000 words at B1+);
    - ≥ 98% coverage relative to *this user's* known items;
    - no proper-noun overload.

    The validator **stores the measured count per target** (instrumentation, brief §11).
- **Volume at the 10/day default:** 2 personal stories per user per day (≈ 14/week, ≈ 14,000 story words/week at B1), as the brief predicts: "more words means more stories, not longer ones". Cost per story is ≈ $0.005–0.01 in LLM tokens plus ≈ $0.02 for WaveNet audio if played (see [08](08-unit-economics.md)).
- **Personal vs shared:**
  - Lesson stories are **personalised** (built around this user's words). They're generated in the overnight batch, at 50% off.
  - Free-reading AI stories (R3) are generated on demand.
  - Popular shared stories are generated once per level and topic, then **re-used** by matching (cheaper, and they can be human-reviewed).
- **Variety controls:** a rotating genre list (mystery, slice of life, sci-fi, workplace drama, sports, romance-light, history), recurring characters for series (bringing readers back), and a ban list of overused LLM tropes ("Once upon a time in a small village…").

## 6.5 YouTube and podcasts

### D6.3 YouTube clips — **Recommendation — needs David's check** (David wants "sometimes a short YouTube section")

**Evidence (YouTube API Services policies)** [S13][S14][S15]:
- Must use the **official embedded player** (IFrame). `start` and `end` parameters are fine, so a 30–90 s segment is OK.
- **Must not modify, obscure or overlay** the player, and must not separate, download or cache audio or video.
- "**API Clients must not … charge users** to view content in an embedded YouTube player." **This rules out YouTube as a paid lesson step.**
- Captions for videos you don't own **can't be downloaded** through the API (`captions.download` needs edit permission on the video). So the transcript has to come from somewhere else.

**Options.**
- **A. Skip YouTube.**
- **B. Free "Watch" extra outside the paywall** (recommended, v1.1):
  - a level-matched clip embedded with start/end times, *next to* (not over) NeuLang's own quiz;
  - available to all users including Lite;
  - the transcript is either (i) **provided by the creator** under a partnership, or (ii) our own STT transcript of the segment, which is a grey area because it requires processing the audio. Don't do (ii) without legal advice.
- **C. Creator partnerships:** ESL YouTubers license their videos and transcripts directly. The cleanest legal route, and it doubles as marketing.

**Filmot check (David's brief asked to verify, not assume).** Filmot indexes YouTube subtitles with keyword, proximity and duration search, which is technically ideal for "find a 3–5 min clip with ~5 of my target words". But, as of October 2026:
- its **official API isn't publicly documented**, and its RapidAPI listing is not open-access (per a 2026 comment reporting a conversation with the developer);
- its terms allow automated access only through documented methods and **grant no commercial-use rights**. They also state that search results **don't license the underlying videos** [S18].

A paid product would need a **direct commercial agreement with Filmot**. Even then, YouTube's "no charging to view embedded content" rule still applies to how clips are shown.

**Feasibility verdict:**
- clip *search* is feasible only with a Filmot deal (or our own index of partnered creators' transcripts);
- clip *display* is feasible only outside the paywall.

**Recommendation:** MVP has **no YouTube**. v1.1 adds free "Watch" extras through C (partnered creators) with the official embed, and asks Filmot about a commercial API in parallel. Never put YouTube inside the paid lesson. The brief's 10/day model assumed 4 clips/day (16 minutes). Without them, encounters come from the second story block and keyed reviews instead ([05 D5.1](05-lessons-and-exercises.md)).

### D6.4 Podcasts — **Recommendation — needs David's check**
- Third-party podcast audio has no general reuse licence. RSS feeds give permission for personal listening apps, not for re-hosting clips inside a paid product.
- **Role (David decided, brief):** podcasts are **listening training, not a vocabulary channel**. They use words the learner already has, and listening events carry little scheduling weight ([04 D4.2](04-review-model.md#d42-initial-weights--recommendation--needs-davids-check-to-be-re-fitted-from-data)).
- **MVP:** NeuLang's own AI mini-podcasts (2-voice TTS, see [05 L4](05-lessons-and-exercises.md#l4--listening-training-mini-podcast--separate-optional)) + **VOA Learning English** audio (public domain).
- **Later:** license clips from ESL podcast creators, or link out to the full episode (a podcast player deep-link) with NeuLang's quiz alongside.

## 6.6 Difficulty scoring

Every text gets a **static** profile when it's ingested and a **personal** score computed at recommendation time.

**Static profile (per text):**
- tokenisation → lemmas + chunks + senses (spaCy-class lemmatiser + chunk matcher from [02](02-english-lexicon-and-languages.md));
- lexical profile: % tokens in each 500-lemma band, % off-list, CEFR-tag distribution;
- syntax: mean sentence length, clause depth, passive rate;
- a **CEFR estimate** from a classifier trained on CEFR-labelled text (e.g. CEFR-annotated corpora such as CEFR-SP for sentences), plus the lexical profile. LLM CEFR simplification and classification are useful but imperfect, especially at A1/A2 [S16][S17]. Use them as one signal, not the decider.

**Personal score (per user × text):**
- **Coverage** = Σ over tokens of P(known) for this user. This is the main signal:
  - **≥ 98%** comfortable;
  - **95–98%** ideal learning zone;
  - **90–95%** "challenge" (offer reader support);
  - **< 90%** don't recommend unless the user explicitly opens it, with support on.
- **Target density:** number of the user's due or about-to-be-forgotten items in the text.

## 6.7 Matching content to users

**Interest model.**
- ~40 topic tags in 8 groups (two-level taxonomy, e.g. *Sports → Football*) plus a **format** tag (news, story, explainer, how-to, opinion, conversation), assigned at ingest by an LLM classifier. The taxonomy is in [13 §13.2](13-interests-and-feedback.md#132-the-interest-taxonomy).
- Plus a text **embedding** for "more like this" and for matching custom interests ("Formula 1").
- The user interest vector is seeded from onboarding (O5) and updated by:
  - the "Was this interesting?" card after every text (L9): 👍/😐/👎, topic bubbles, reason chips, free text;
  - read-through rate;
  - time on text.

  Update sizes, decay and the ~15% exploration share are in [13 §13.6](13-interests-and-feedback.md#136-how-each-signal-changes-the-users-interest-weights). The same weights also shape **which words are taught** ([12 §12.5](12-vocabulary-database.md#125-how-interests-change-what-counts-as-most-common)).

**Recommendation score** (initial hand-tuned weights; later learned from read-through):
```
score = 0.35·interest_match + 0.30·level_fit(coverage; peak at 96–98%) + 0.20·target_density
      + 0.10·freshness + 0.05·diversity_bonus  − penalties(already read, same source 3× this week)
```
- **"Articles with your words"** (David's): rank by target density among texts with coverage ≥ 93%.
- **Cold start:** the first week uses onboarding interests and a popular-per-level fallback.

## 6.8 QA and safety chain for generated content

Each generated text passes, in order:
1. **Lexical validator** (deterministic): coverage within target, each new target ≥ 3× (§6.4), new-target density ≤ 1.5%, each target in its intended sense, no off-list words above the allowed rate. Tested with a seeded-error suite ([14 §14.7](14-gaps-and-test-plans.md#147-test-plan-story-generation-and-the-validator)). Fail → regenerate with the problems listed (max 2 retries) → fall back to a corpus text.
2. **Moderation** (provider moderation endpoint + rules): no sexual content, graphic violence, self-harm, hate, or medical/legal advice. Since the app may be used by teens, the default setting is "general audience".
3. **Factuality** (news explainers only): claim-by-claim check against the sources.
4. **Overlap check** (explainers and simplified CC texts): n-gram overlap with the source, per §6.3.
5. **Naturalness judge** on a sample (~5% of personal stories, 100% of shared stories): a stronger model rates fluency and level appropriateness. A human reviews anything flagged.
6. **Labelling:** AI-generated / AI-adapted label + attribution on R4.
7. **User reports** (R5/E6) feed a review queue. ≥ 3 reports → auto-hide.
8. **Native-speaker content audit** (brief: "content error rate > 2% is a fail"):
   - a native English-speaking reviewer audits a random weekly sample, stratified by content type (AI story, explainer, adapted text, cloze sentence, gloss);
   - each item is labelled for language errors, wrong glosses and factual errors;
   - the audited **content error rate must stay ≤ 2%** per content type;
   - above that, the type is paused from new lessons until prompts or sources are fixed.
   - Sample size: ~300 items per type per audit gives a ±1.6-point margin at 2% (95% confidence), enough to tell 2% from 4%.

### D6.5 AI-content transparency and a "real texts" preference — **Recommendation — needs David's check**
- **Evidence (brief):** the paying segment is wary of AI content. The category's most-upvoted new entrant markets itself on having "no AI content" (1.6k upvotes). The closest competitor, Clozemaster, uses human-written Tatoeba sentences.
- **Recommendation:**
  - keep David's content mix, but **label every AI-generated text** (also legally required for public-interest text under the EU AI Act);
  - prefer human-written sentences for cloze (Tatoeba, [05 L3c](05-lessons-and-exercises.md#l3c--cloze-check-david-decided-modality-fill-in-the-blank));
  - add a Settings preference "**Prefer real texts**", which raises the share of public-domain, CC and as-is texts in recommendations and excerpt-mode lesson blocks.

  AI stories remain the default lesson engine because only they can guarantee 3× repetition at ≤ 1.5% density.

## 6.9 Launch corpus target

- **~1,500 shared texts:** 40 topics × 3 level bands (A2 / B1 / B2+) × ~12 texts.
- Mix: ~60% AI-original stories and explainers, ~25% VOA/public-domain/CC adapted, ~15% as-is real texts.
- Plus personalised lesson stories generated per user.
- Generation cost: about 1,500 texts × ~2k output tokens ≈ 3M output tokens, i.e. a few dollars on a small model (see [08](08-unit-economics.md)). **Review time is the real cost, not tokens.**

---

## Sources
- [S1] *Thomson Reuters v. Ross Intelligence*, 3rd Cir. affirmance (30 Sep 2026); see district court opinion D. Del. Feb 2025 — https://www.ded.uscourts.gov/
- [S2] *Kadrey v. Meta* (N.D. Cal. 2025), discussion of market substitution
- [S3] *The New York Times Co. v. Microsoft & OpenAI* (S.D.N.Y.), ruling on motions to dismiss (2025)
- [S4] Gannett / USA Today v. OpenAI, complaint filed 8 Oct 2026
- [S5] Directive (EU) 2019/790 (DSM Directive), Arts. 4 and 15 — https://eur-lex.europa.eu/eli/dir/2019/790/oj
- [S6] US Copyright Office, *Copyright and Artificial Intelligence, Part 2: Copyrightability* (Jan 2025) — https://www.copyright.gov/ai/
- [S7] VOA Learning English, terms of use (public domain; credit; excludes AP/Reuters/AFP) — https://learningenglish.voanews.com/p/6861.html
- [S8] Status of VOA after 2025 cuts — https://www.savevoa.com/
- [S9] Global Voices, Creative Commons licence — https://globalvoices.org/about/ (site footer: CC BY 3.0)
- [S10] The Conversation, republishing guidelines (CC BY-ND) — https://theconversation.com/republishing-guidelines
- [S11] Creative Commons BY 4.0 deed — https://creativecommons.org/licenses/by/4.0/
- [S12] EU AI Act, Article 50 (transparency obligations) — https://ai-act-service-desk.ec.europa.eu/en/ai-act/article-50
- [S13] YouTube API Services Developer Policies — https://developers.google.com/youtube/terms/developer-policies
- [S14] YouTube Required Minimum Functionality — https://developers.google.com/youtube/terms/required-minimum-functionality
- [S15] YouTube Data API `captions.download` — https://developers.google.com/youtube/v3/docs/captions/download
- [S16] TSAR 2025 shared task on CEFR-targeted simplification (LLMs work but are imperfect at A1/A2)
- [S17] COLING 2025 paper on LLM readability-controlled simplification — https://aclanthology.org/2025.coling-main.452/
- [S18] Filmot terms of service — https://apiv1.filmot.com/terms ; API access status — https://github.com/dusking/filmot/issues/2
- [S19] Tatoeba terms of use (CC BY 2.0 FR) — https://tatoeba.org/en/terms_of_use
- David's planning brief (2026-10-13) — [`internal/source/neulex-planning-brief.md`](../../internal/source/neulex-planning-brief.md)
