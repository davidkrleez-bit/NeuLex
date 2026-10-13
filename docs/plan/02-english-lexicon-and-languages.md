# 02 · ESL-first: native languages, word list, chunks, CEFR

[← Back to plan index](../neulang-plan.md)

Making English the first target (**David decided**) changes three things:
1. **The native language becomes the variable.** Glosses, placement distractors, false-friend lists and prompts all depend on it.
2. **The word list must be English-specific and legally reusable.**
3. **The progress metric needs real coverage research behind it.**

---

## 2.1 Native languages at launch

> **Conflict to resolve (brief vs project context).** David's planning brief suggests **Japanese-first** as the *target* language. Its argument comes from r/Refold: sentence mining is most valuable where direct translations and free frequency decks are weak, and European languages have plenty of both. The project context records **ESL first** as David's decision, so this plan keeps ESL.
>
> The brief's argument applies to English too: free English frequency decks and graded readers are abundant. So ESL-first raises the bar on differentiation: interest-matched content, tracked state, and native-language-specific glosses. This doesn't change the plan, but it belongs in the positioning ([11](11-distribution-and-positioning.md)).

### D2.1 Which native languages to support first — **Recommendation — needs David's check**

**Options.**
- **A. One native language** (e.g. Spanish only). Easiest to do well, but the smallest market.
- **B. Two high-volume native languages plus an English-only mode** (recommended: Spanish + Brazilian Portuguese).
- **C. 6–10 native languages with machine-translated glosses.** Broad reach, but quality risk in the exact feature (glosses, placement distractors) that makes the product trustworthy.

**Evidence.**
- Demand: English is the most-studied language on Duolingo in 154 countries (79% of countries) in 2025 [S1]. English-from-Spanish has historically been Duolingo's single largest course (27.4M learners in 2019) [S2]. Duolingo's English-only course became the 2nd most popular English course in China, Colombia, Mexico and the US [S1], which shows real demand for an English-only mode.
- Revenue: 2025 language-app revenue estimates: US $1.25B, China $653M, Japan $383M, India $328M, Korea $160M, Brazil $154M [S3].
  - High-paying markets (Japan, Korea) need high-quality localisation.
  - China has app-distribution and payment barriers.
  - Latin America and Brazil are large and Spanish/Portuguese are closely related, so one QA workflow covers both.
- The per-native-language cost is real work, not just UI strings. Each native language needs:
  - **reviewed glosses** for ~3,500 core items plus chunks;
  - **placement multiple-choice distractors** in the native language;
  - a **false-friend / cognate list**, because cognates shift item difficulty a lot — for Spanish-native learners, cognate status is the second-strongest predictor of word difficulty after frequency [S4];
  - **IRT calibration of the placement test for that native language**;
  - LLM prompts that know the native language;
  - a localised store listing and paywall.
- Price sensitivity: $8/month is well below LingQ ($14.99), Migaku ($15) and Speak ($17.99) [see 08], but high relative to Latin American income. Regional pricing applies (see [08](08-unit-economics.md)).

**Recommendation: B.**
- **Launch with Spanish (Latin American neutral) and Brazilian Portuguese** as fully supported native languages: human-reviewed glosses, localised UI, native-language-specific placement calibration.
- Add **English-only mode** for any native language at B1+: English definitions with simple wording, no translations. This also captures demand from markets you don't support yet.
- Collect "my language isn't here" waitlist signups and add the next 2–3 native languages (candidates: Turkish, Vietnamese, Japanese, Korean, Arabic) based on that data.
- **If David's own audience or network mostly speaks a different language, that should override this.** This is a key question for him.

### D2.2 Gloss source — **Recommendation — needs David's check**
- Generate glosses with an LLM **per sense, in context**: one gloss per sense ID, not per spelling.
- Use a human reviewer per native language for the core ~3,500 items. That is a bounded one-off task of about 3,500 short entries per native language.
- Gloss rare words in the long tail on the fly, cache them, and mark them "auto".
- Never use a single dictionary-style gloss for a polysemous word in the reader. The reader resolves the sense from sentence context (an LLM call when the text is ingested, cached per text).

---

## 2.2 The word list

### D2.3 Counting unit: lemma vs word family — **Recommendation — needs David's check**

**Options.**
- **Word family** (Level 6: *decide, decision, decisive, indecisive…*). Used in classic coverage studies.
- **Lemma** (*decide, decides, decided, deciding*; noun and verb separate). NGSL's unit.
- **Flemma** (same spelling regardless of part of speech).

**Evidence.**
- Coverage studies count in families. 98% coverage of written text needs ~8–9k families, spoken ~6–7k [S5]. 95% ≈ 4–5k families, 98% ≈ 8k families [S6].
- But learners, especially at lower levels, don't reliably know derived forms just because they know the base word. Lemma-based lists exist for this reason [S7]. NGSL is lemma-based [S8].

**Recommendation.**
- **The lemma (plus part of speech) is the learnable item.** Store family links so that:
  - inflections always credit the lemma;
  - transparent derivations (e.g. *-ly*, *-ness*) give a **partial** passive encounter credit to the base (×0.5);
  - non-transparent derivations are separate items.
- Coverage research numbers are converted carefully in user-facing copy.

### D2.4 Source lists — **Recommendation — needs David's check**

| List | Size | What it's for | Licence / cost | Use? |
|---|---|---|---|---|
| **NGSL 1.2** | 2,809 lemmas, ~92% coverage of general English [S8] | Core spine | Free, incl. commercial, with citation [S8] | ✅ Core |
| **NGSL-Spoken** | ~720 lemmas | Speaking/listening priority | Same | ✅ Boost spoken words |
| **NAWL** | ~960 academic lemmas | "Study" goal | Same family of lists | ✅ Goal-based |
| **TSL / BSL** | TOEIC / business | "Work" goal | Same | ✅ Goal-based |
| **CEFR-J wordlist** (A1–B2) + **Octanove C1/C2** | ~7,800 | CEFR labels | CEFR-J free for commercial use with citation; Octanove CC BY-SA [S9] | ✅ CEFR tags |
| **SUBTLEX-US** | 74k words, subtitle frequency | Long-tail ranking, spoken bias | CC BY-SA [S10] | ✅ Ranking beyond NGSL |
| **COCA frequency** | 60k+ | Ranking | **$295 commercial, can't redistribute, frequency may only be shown in ≤20 bands** [S11] | ❌ Avoid |
| **Oxford 3000/5000** | 5k, CEFR-tagged | — | Proprietary | ❌ Don't copy |

**Licence notes.**
- CC BY-SA lists (SUBTLEX-US, Octanove): if NeuLang **redistributes the list itself or a derived list**, share-alike applies to that list.
- Using the lists only to rank and tag your own items internally is a lower-risk use.
- Include the attributions in the Licences screen (P3). Ask a lawyer to confirm how share-alike applies to a derived ranked database.

### D2.5 Programme size and the "% of everyday speech" metric — **Recommendation — needs David's check**

**David's original idea:** "top 3,000 words" and "% of everyday speech understood".

**Evidence.**
- NGSL's ~2,800 lemmas cover ~92% of general text, and ~95% of TV dialogue such as *Friends* [S8].
- Adequate unassisted comprehension needs ~98% coverage; 95% is the minimum for reading with some support [S6][S12].
- So 3,000 words is a strong first milestone, but **not** "understand everyday English". Users at 92% coverage meet an unknown word about every 12 words.

**Recommendation** (updated with David's planning brief, 2026-10-13: "a flat top 3000 is too coarse"; "count, don't rate"; "never put a coverage % next to a one-month horizon").
1. **Core programme ≈ 4,000 lemmas plus ~600 chunks**, ordered by NGSL rank, adjusted by goal list and by the user's interest topics (D2.8). **Personalised by gap, not by list:** the app teaches *the high-frequency words this learner doesn't know yet*.
2. **Headline progress = counts, not percentages.** "**214 words learned this month · on track for 3,650 this year**" at the 10/day default. The brief's arithmetic: 10/day = 70/week, ~300/month, 3,650/year.
3. **Coverage % only at 6–12-month horizons**, and on the placement results screen (a current level, not a delta). The coverage curve is logarithmic, so a one-month delta is small and easy to disprove. Wording: **"You know X% of the words in everyday English"**, never "% understood": coverage ≠ comprehension.
4. **When citing coverage research, state that it counts word families** and convert honestly (NeuLang's unit is the lemma, D2.3). Copy-safe facts:
   - 95% coverage of spoken English (adequate *listening*) ≈ **2,000–3,000 word families** (van Zeeland & Schmitt 2013 [S16]);
   - 98% of written text ≈ 8,000–9,000 families (Nation 2006 [S5]).
   - **Don't cite:** "1,000 words → 80% coverage / 55% comprehension" (the 55% is likely a test pass mark, not comprehension) or "audio ≈ half the vocabulary yield of reading" (unsourced).
5. Proper nouns and transparent words count as known (they add 2–3% coverage [S6]). **Function words count as known** for coverage but are never study items (D2.9).
6. Coverage is computed from **P(known)** per item (see [03](03-placement-test.md), [04](04-review-model.md)), not just from words marked as learned.

### D2.8 Bands and domain weighting (granularity) — **Recommendation — needs David's check**
The brief rejects a flat "top 3000" as too coarse. Instead, use bands that mean something to a learner:

| Band (shown to user) | Lemmas | Roughly |
|---|---|---|
| Core 1 · "Survival" | 1–500 | A1 |
| Core 2 · "Everyday" | 501–1,000 | A1–A2 |
| Core 3 · "Conversations" | 1,001–2,000 | A2–B1 |
| Core 4 · "Listening-ready" | 2,001–3,000 | B1 (≈ 95% spoken coverage) |
| Core 5 · "Reading-ready" | 3,001–4,000 | B1–B2 |
| Domain packs | Work (TSL/BSL), Study (NAWL), and topic packs by interest | Any |

- Each band has 5 sub-bands of 100–200 lemmas, used for progress bars ("Core 3: 64%").
- **Domain weighting:** each lemma gets a per-topic frequency from the topic-tagged corpus ([06 §6.7](06-content-pipeline.md#67-matching-content-to-users)). The user's **priority score** = global frequency rank, boosted by keyness (how much more frequent the word is in the user's interest topics than in general English), so *pitch* or *transfer* rise for football fans.
- The boost is capped so a learner never skips a Core band item for a niche word more than ~1 in 5 times.

### D2.9 Function words are never study items — **David decided** (planning brief)
- Closed-class words (articles, pronouns, prepositions, conjunctions, auxiliaries, determiners; about 300 lemmas, flagged `is_function_word`) appear naturally in texts but are **never** triaged, taught or scheduled. They're acquired implicitly.
- Chunks that contain function words (*as well as*, *in terms of*) are still items (D2.6).

### D2.10 Word-selection loop: prediction + one-tap correction — **Recommendation — needs David's check**
- **Prediction:** P(known) per item comes from the placement IRT model ([03](03-placement-test.md)): frequency band, CEFR, cognate status for the learner's native language, and the topic gap profile.
- **Cold start:** the placement test. If it was skipped, use the self-rated level plus band priors, marked low-confidence.
- **Correction, in the reader:**
  - words predicted unknown are pre-glossed or underlined; a long-press gives "**I know this**" (→ quick keyed check on the next speed round, then Known);
  - words predicted known are plain; **tapping one is the demotion signal** (David decided: re-tapping a word previously marked known). It counts as Again for taught items and drops P(known) for untaught ones.
- **Confidence handling:** items with P between 0.35 and 0.75 are "uncertain". The planner samples 1–2 of them per block as triage cards, so the model learns where it's least sure.
- **Rejected (David decided):** requiring a production per word to prove acquisition, or AI-driven demotion.

---

## 2.3 Multi-word chunks

**David decided** (original write-up): chunks count as items.

### D2.6 Which chunks and how they're counted — **Recommendation — needs David's check**

**Sources.**
- **PHRASE List**: 505 frequent, non-transparent multi-word expressions (e.g. *as well as*, *in terms of*, *take part*) [S13].
- **PHaVE List**: 150 phrasal verbs with their 288 most frequent senses, covering 75%+ of occurrences [S14].
- Plus ~200 curated discourse markers and conversational formulas: *by the way*, *I mean*, *no wonder*, *it's up to you*.

**Recommendation.**
- **Unit:** each chunk is its own item, with its own sense ID, gloss and FSRS state.
- **Detection (tokeniser):**
  - greedy longest-match on lemmatised tokens;
  - allow gaps of up to 3 tokens for separable phrasal verbs ("pick *it* up");
  - an LLM confirms the sense for ambiguous matches (*look up* a word vs *look up* at the sky) when the text is ingested.
- **Crediting component words:** an opaque chunk (*give up*) doesn't credit *give* or *up*. A transparent collocation (*heavy rain*) credits both words with a passive encounter.
- **Introduction rate:** chunks start at about A2/B1. Roughly 1 of the 5 daily new items is a chunk from B1 up. Chunks count toward the 5/day.
- **Exercises:** chunks are best practised in chat and opinion steps, and are weak in picture steps.

---

## 2.4 CEFR mapping

### D2.7 How to label user level — **Recommendation — needs David's check**

**Evidence.**
- Milton (2010) maps X-Lex vocabulary scores (out of the 5,000 most frequent lemmas) to CEFR [S15]:

| CEFR | Approx. known among top 5,000 lemmas |
|---|---|
| A1 | < 1,500 |
| A2 | 1,500–2,500 |
| B1 | 2,500–3,250 |
| B2 | 3,250–3,750 |
| C1 | 3,750–4,500 |
| C2 | 4,500–5,000 |

- CEFR-J and Octanove give per-word CEFR labels [S9].

**Recommendation.**
- Show a **"vocabulary level ≈ B1"** label derived from Milton's bands, always worded as **vocabulary** level. NeuLang doesn't test grammar, speaking or writing, so it must not claim an overall CEFR level.
- Use per-word CEFR tags to:
  - target story generation (e.g. "B1 story, ≤3% words above B1");
  - pick placement items;
  - pick the "next" band to probe.

---

## Sources
- [S1] Duolingo Language Report 2025 — https://blog.duolingo.com/2025-duolingo-language-report/
- [S2] Duolingo Language Report 2019 (largest course: English from Spanish) — https://blog.duolingo.com/2019-duolingo-language-report/
- [S3] Appfigures / Business of Apps, language-learning app revenue 2025 — https://www.businessofapps.com/data/language-learning-app-market/
- [S4] Urdaniz & Skoufaki, "Spanish L1 EFL learners' recognition knowledge of English academic vocabulary: the role of cognateness, word frequency and length", *Applied Linguistics Review* (cognateness = 2nd predictor after frequency)
- [S5] Nation (2006), "How large a vocabulary is needed for reading and listening?" — https://www.lextutor.ca/cover/papers/nation_2006.pdf
- [S6] Laufer & Ravenhorst-Kalovski (2010), "Lexical threshold revisited", *Reading in a Foreign Language* 22(1) — https://nflrc.hawaii.edu/rfl/item/203
- [S7] Brezina & Gablasova (2015), "Is there a core general vocabulary?" *Applied Linguistics* — https://doi.org/10.1093/applin/amt018
- [S8] New General Service List (NGSL 1.2) — https://www.newgeneralservicelist.com/
- [S9] Open Language Profiles: CEFR-J + Octanove wordlists — https://github.com/openlanguageprofiles/olp-en-cefrj
- [S10] SUBTLEX-US (Brysbaert & New) — https://www.ugent.be/pp/experimentele-psychologie/en/research/documents/subtlexus
- [S11] wordfrequency.info licence (COCA) — https://www.wordfrequency.info/license.asp
- [S12] Hu & Nation (2000), "Unknown vocabulary density and reading comprehension", *Reading in a Foreign Language* 13(1)
- [S13] Martinez & Schmitt (2012), PHRASE List — https://www.lextutor.ca/freq/lists_download/phrase_list_martinez.htm
- [S14] Garnier & Schmitt (2015), PHaVE List — https://www.norbertschmitt.co.uk/vocabulary-resources
- [S16] van Zeeland & Schmitt (2013), "Lexical coverage in L1 and L2 listening comprehension: the same or different from reading comprehension?", *Applied Linguistics* 34(4), 457–479 — https://doi.org/10.1093/applin/ams074
- David's planning brief (2026-10-13) — [`internal/source/neulex-planning-brief.md`](../../internal/source/neulex-planning-brief.md)
- [S15] Milton (2010), "The development of vocabulary breadth across the CEFR levels", EUROSLA Monographs 1 — https://www.eurosla.org/monographs/EM01/211-232Milton.pdf
