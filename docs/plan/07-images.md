# 07 · Images: options, licensing, priority

[← Back to plan index](../neulang-plan.md)

**David decided:** picking a picture for a word is valuable. The **source** (possibly scraped stock images) and the **priority** are undecided. This section answers both. Everything here is **Recommendation — needs David's check**.

---

## 7.1 Where images appear

There are two different jobs:
1. **Anchor images** (L2, "pick 1 of 4"): simple, recognisable pictures of a word's meaning. About **1,500 imageable items** in the 4,000-lemma programme (concreteness ≥ 3.5 [S1]), × 4 candidate images ≈ 6,000 images.
2. **Scene images** (L7, picture description): a *situation* that shows one or more words in action (David's example: a student copying another's test for *cheating*). These work for many abstract-but-situational words (*argue, borrow, celebrate, ignore*). About **1,000–1,500 scenes**, each tagged with the 1–4 words it shows.

## 7.2 Options

### D7.1 Image source — **Recommendation — needs David's check**

| Option | Cost | Licence / terms | Fit |
|---|---|---|---|
| **Scrape stock / Google Images** | $0 | **Copyright infringement.** Watermarked and rights-managed images; no licence for commercial use. Getty actively sues. | ❌ Don't do it |
| **Unsplash API** | Free | Must **hotlink** images from Unsplash's URLs (no re-hosting), **attribute photographer + Unsplash with UTM links**, trigger the download endpoint; 50 req/h demo, 1,000 req/h production after approval [S2] | ✅ Anchors for concrete nouns; ⚠️ hotlinking means no offline use unless the API terms allow caching |
| **Pexels API** | Free | Must show a **prominent link to Pexels** and credit photographers where possible; 200 req/h, 20k/month, higher limits on request [S3] | ✅ Anchors |
| **AI-generated (GPT Image 1 Mini)** | **$0.005 low / $0.011 medium / $0.036 high** per 1024² image [S4] | Output is owned by the user under OpenAI's terms; not copyrightable in the US (no exclusivity) | ✅ Scenes; ✅ consistent style for anchors |
| **AI-generated (Gemini 3.1 Flash Image)** | ~$0.067 per image (50% off in batch); Flash-Lite Image ~$0.034 [S5] | Similar; SynthID watermark | ✅ Alternative |
| Illustrator / commissioned set | $5–$30+ per illustration | Full ownership | Later, for a signature style |
| Open-licence icon sets (e.g. OpenMoji CC BY-SA, Twemoji CC BY) | Free | Attribution / share-alike | Fallback for very simple concrete nouns |

*Note: Google's Imagen 4 was shut down on 17 Aug 2026, so it's not an option.*

**Recommendation.**
- **Scenes → AI-generated, pre-made and human-reviewed**, in one consistent illustration style.
  - 1,500 scenes × ~3 attempts at medium quality ≈ 4,500 × $0.011 ≈ **$50**.
  - Review is the real cost. A reviewer checks that the scene is clear, culturally neutral, free of text artefacts and safe.
- **Anchors → AI-generated as well, as the primary source:**
  - 6,000 × $0.011 ≈ **$66** at medium, or **$30** at low quality.
  - **Unsplash/Pexels add photo variety** for concrete nouns, following their attribution and hotlinking rules.
  - Reasons to prefer AI here: (a) you can store and cache them, which matters for offline lessons; (b) one consistent style; (c) you can depict exactly the sense (*bat* the animal vs *bat* the stick).
- **Never scrape.**
- **No per-user generation at launch.** Shared, pre-made images keep the cost at a one-off ~$100 and make review possible.

## 7.3 Priority

### D7.2 How important are images — **Recommendation — needs David's check**

**Evidence.**
- Pictures can **help or hurt** vocabulary learning. Carpenter & Olson (2012) found that pictures made learners **overconfident** and didn't improve foreign-word learning unless that overconfidence was removed. Pictures helped when learners didn't over-rely on them [S6]. **Images are worth it only if retrieval practice follows**, which NeuLang's speed round provides.
- Dual coding (verbal + image) is a well-established memory aid for **concrete** words, and much weaker for abstract words.
- Picture description is one of David's 3 "use it" exercises. Without scene images, that exercise doesn't exist.

**Recommendation: medium priority. Ship in the MVP, but cheaply and pre-made.**
- **MVP:** the scene library for picture description (needed for one of David's exercise types) + anchor images for the top ~800 imageable words. Words without images get a text anchor (gloss + example + a quick "which sentence fits?").
- **v1.1:** extend anchors to all imageable words; let users choose the image (David's "pick 1 of 4") and show it as the word's personal image in the Vault and the speed round.
- **Track whether it helps:** compare recall on the first speed round for words anchored with an image vs text-only (an A/B test at word level). If images don't improve recall, keep them for delight but don't invest more.

## 7.4 Image quality and safety checklist
- Shows **one** clear sense. No text or letters in the image (AI tools often garble text).
- Diverse people; culturally neutral where possible; no stereotypes (check e.g. *poor*, *criminal*, *nurse*).
- No brand logos or recognisable public figures.
- Safe for teens. *Cheating* is fine; *drunk* or *weapon* need care.
- Alt-text stored for each image (accessibility + grading context in L7).
- Attribution stored for Unsplash/Pexels images and shown on tap.

---

## Sources
- [S1] Brysbaert, Warriner & Kuperman (2014), concreteness ratings — https://doi.org/10.3758/s13428-013-0403-5
- [S2] Unsplash API guidelines (hotlinking, attribution, rate limits) — https://help.unsplash.com/en/articles/2511245-unsplash-api-guidelines
- [S3] Pexels API documentation and guidelines — https://www.pexels.com/api/documentation/
- [S4] OpenAI API pricing, GPT Image 1 Mini — https://openai.com/api/pricing/
- [S5] Google Gemini API pricing (image models) — https://ai.google.dev/gemini-api/docs/pricing
- [S6] Carpenter & Olson (2012), "Are pictures good for learning new vocabulary in a foreign language? Only if you think they are not", *JEP: Learning, Memory, and Cognition* 38(1), 92–101
