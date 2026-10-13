# 12 · The vocabulary database: building, ranking and maintaining words and phrases

[← Back to plan index](../neulang-plan.md) · Plain-language version: [00 How it all works](00-how-it-all-works.md)

**David decided (2026-10-13):**
- Common phrases must be in the programme.
- Phrases must be ranked **against** single words by how common they are, rather than added at a fixed quota.
- What counts as "most common" must depend on the kind of texts each user is interested in.

Everything else here is **Recommendation — needs David's check**.

---

## 12.1 What the database is

One table of **items**. An item is anything a learner can learn and be tested on:

| Item kind | Examples | Unit |
|---|---|---|
| **Word** | *decide*, *pollution*, *however* | Lemma + part of speech (*decide* covers decides/decided/deciding) |
| **Word sense** (only for very common polysemous words) | *bank* (money) vs *bank* (river) | One sense = one learnable item when the second sense is frequent enough |
| **Phrase** | *give up*, *at least*, *by the way*, *make a decision* | A pattern: the words, allowed inflections, and allowed gaps ("give *it* up") |

Each item carries:
- frequencies (overall, spoken, written, and per topic);
- categories;
- CEFR level;
- per-native-language data (gloss, cognate or false-friend flag, "trap" flag);
- live statistics from users.

Users never see "the database". They see items appear in triage, stories, the Vault and their progress.

## 12.2 Step 1 — getting the first list of most frequent words

**Principle: frequency from real, human-written English, measured across many kinds of text, not one.**

### Sources

| Source | What it gives | Licence | Role |
|---|---|---|---|
| **NGSL 1.2** (2,809 lemmas) | An expert-built core list based on a 273M-word corpus, selected for frequency **and** spread across text types | Free incl. commercial, with citation | The **spine** and sanity check for the top ~2,800 |
| **SUBTLEX-US** | Word frequencies in film/TV subtitles, the best predictor of everyday spoken familiarity | CC BY-SA | Spoken frequency |
| **wordfreq** (snapshot) | Frequencies blended from Wikipedia, subtitles, news, books and web, through about 2021 | CC BY-SA 4.0 | Written/general frequency for the long tail |
| **NeuLang's own human-written corpus**, split by topic (VOA archive, Wikinews, Wikipedia by category, Global Voices, Gutenberg, US government texts; see [06](06-content-pipeline.md)) | Frequencies **per topic**, and phrase counts | Per source | Topic weighting (§12.5) and phrase counting |
| **CEFR-J + Octanove** | CEFR level per word | Free / CC BY-SA | Labels, not ranking |
| PHRASE List, PHaVE List | Phrase seeds with frequencies (§12.3) | Free for research/teaching; check commercial terms with the authors | Phrase spine |

> **Important maintenance lesson.** wordfreq's author **stopped updating it in 2024** because generative AI text had polluted the web data: *"I don't think anyone has reliable information about post-2021 language usage by humans"* [S1]. NeuLang will itself generate a lot of AI text. **AI-generated text must never be counted in our frequency data**, or the list drifts toward whatever words LLMs overuse. Every corpus row carries a `human_written` flag, and frequency jobs only read flagged rows.

### Build pipeline (a batch job that produces a versioned "lexicon release")
1. **Normalise:** lemmatise and POS-tag every source with the same tool (spaCy-class), merge US/UK spellings (*color/colour*), and drop proper nouns, numbers and tokenisation junk.
2. **Count** per corpus: frequency per million words, plus **dispersion** (how evenly a word spreads across documents and topics). A word that's frequent only in a few documents (*hobbit*) shouldn't rank as core. Use a standard dispersion measure (e.g. Gries' DP).
3. **General score** per lemma = a weighted average of its **Zipf** values (log₁₀ frequency per billion words; 7 = extremely common, 3 = rare [S2]):
   - spoken (SUBTLEX) 40%, written (wordfreq) 40%, NeuLang corpus 20%;
   - multiplied by a dispersion penalty.
4. **Reconcile with NGSL:** where our rank and NGSL's disagree by more than ~1,500 places in the top 3,000, a reviewer decides. NGSL is the expert prior; big disagreements are usually tokenisation bugs.
5. **Flag function words** (~300 closed-class lemmas). They're never study items (David decided).
6. **Split senses** for the ~300 most polysemous high-frequency words: an LLM tags senses in a corpus sample, a human checks, and each sense's share of uses is recorded. A second sense becomes its own item only if its own frequency would rank it inside the programme.
7. **Attach CEFR tags and per-native-language data:** glosses, cognate / false-friend flags, and an "L1 trap" flag for phrases that translate badly (§12.3).
8. **QA and publish** `lexicon_version = N`. The release log records exactly which source snapshots were used.

**Output size at launch:** ~4,000 words (+ ~300 split senses) and ~600–800 phrases, ranked together (§12.4). That's enough to take a learner to ~95% coverage of everyday text ([02](02-english-lexicon-and-languages.md)).

## 12.3 Phrases: what counts, where they come from, how they're categorised

### Why phrases matter (evidence)
- Martinez & Schmitt built the **PHRASE List** (505 non-transparent multiword expressions) by comparing BNC n-gram counts with single-word counts [S3]:
  - merged into the top 5,000 word families, phrases make up **over 10% of all items**;
  - **95% of the words inside these phrases come from the top 1,000 words**. Learners *think* they understand *at least* or *come to* because they know each word. The authors call this being "deceptively transparent".
- **Phrasal verbs alone occur about once every 150 words**, more often than *are*, *but* or *they*. The top 100 phrasal verbs cover over half of all phrasal-verb uses [S4].

**Design consequence:** phrase knowledge **can't be inferred from word knowledge**. A learner who knows *give* and *up* doesn't necessarily know *give up*. Phrases get their own items, placement probes ([03](03-placement-test.md)) and states.

### Phrase sources
1. **PHRASE List** (505): general expressions (*have to*, *such as*, *of course*, *at least*, *I mean*, *at the moment*), with frequencies per 100M words and genre marks [S3].
2. **PHaVE List** (150 phrasal verbs, 288 senses): *pick up*, *figure out*, *turn out*.
3. **Spoken formulas** (~150, curated from subtitle n-grams): *how's it going*, *no worries*, *I'm good*, *you know what*, *it's up to you*.
4. **Academic Formulas List** for the Study goal (*on the other hand*, *in terms of*, *as a result*).
5. **L1-trap collocations** per native language (~100 each). These are phrases whose translation is a different verb or structure, e.g. Spanish/Portuguese speakers say *"make a question"* (from *hacer/fazer una pregunta*) instead of *ask a question*, or *"take a decision"* where English prefers *make a decision*. Found by comparing English collocations with literal translations (LLM-assisted, human-checked).
6. **Data-driven candidates** from NeuLang's corpus: 2–4-word n-grams that are frequent and strongly associated (high mutual information or ΔP). An LLM rates **opacity**, a reviewer approves. This is also how new phrases enter later (§12.7).

**Excluded:** fully transparent combinations (*in the morning*, *very good*). Knowing the words is enough, so teaching them wastes a slot.

### Phrase categories (stored as tags, used for filtering, UI and balance)

| Dimension | Values | Used for |
|---|---|---|
| **Type** | phrasal verb · fixed expression · discourse marker · spoken formula · collocation · compound noun · idiom | Reader detection rules, exercise choice (spoken formulas suit chat; collocations suit cloze) |
| **Function** | opinion (*I reckon*), sequencing (*after that*), contrast (*on the other hand*), time (*at the moment*), quantity (*a few*), social (*no worries*), hedging (*kind of*) | Grouping in the Vault ("Phrases for giving opinions"), topic-coherent blocks |
| **Opacity** | opaque (1.0) · semi (0.5) · transparent (0, excluded) | Ranking (§12.4) |
| **Register** | spoken · written · academic · both | Goal weighting |
| **CEFR** | A1–C2 | Labels, level fit |
| **L1 trap** | per native language: yes/no + note | Ranking boost + feedback text ("Careful: in English we *make* a decision") |
| **Separable** | yes/no + max gap | Reader matching ("pick *the kids* up") |

## 12.4 Ranking words and phrases on one scale

**David's requirement:** rank phrases against words by how common they are; don't hard-code "5 + 1".

### The unified score: expected coverage gain

For learner *u* and item *i*:

```
gain(i, u) = f_u(i) × opacity(i) × (1 − P_known(u, i))
```

- **f_u(i)** = how often the item occurs, per million words, **in the kind of English this learner reads and hears** (§12.5).
  - Phrases are counted as whole units, only in their idiomatic sense: *so far* = "until now", not "a long distance".
  - Words are counted as lemmas (or senses).
  - Both come from the **same corpora**, so the numbers are directly comparable. The PHRASE List was built exactly this way, and its "integrated rank" merges phrases into the word list [S3].
- **opacity(i)** = 1 for words and opaque phrases, 0.5 for semi-transparent ones. A phrase you can mostly work out from its words is worth less.
- **1 − P_known** = how likely the learner *doesn't* know it yet (from placement and later signals; [03](03-placement-test.md)).

In plain terms: **"How many more words of your kind of text will you understand after learning this item?"** The score ranks *at least* and *pollution* on the same ruler.

**Real figures from the PHRASE List (per 100 million words, BNC)** [S3]:
- *have to* 83,092 → merged rank **107**, as common as a top-110 word;
- *such as* 30,857 → rank 415;
- *of course* 26,966 → rank 483;
- *I mean* 23,616 → rank 556;
- *at the moment* 5,001 → rank **1,959**, sitting next to words around rank 2,000.

### Small adjustments on top of the score
- **L1 trap phrase:** × 1.3 for that native language, because those phrases cause errors even when the learner "knows" the words.
- **Goal:** the Conversation goal weights spoken frequency more (§12.5); the Study goal boosts academic formulas.
- **Recently looked up:** × 1.5 for an item the learner tapped in reading in the last 7 days. It's clearly relevant to them, and it closes the reader → lesson loop.
- **Eligibility for phrases:** the phrase's component words must be at least "probably known" (P ≥ 0.6). *Pick up* waits until *pick* is known. This prevents double unknowns in one item.

### What mix this produces (answering "5 + 1 or 4 + 1?")

Martinez & Schmitt's merged list shows **how the phrase share grows with level** [S3]:

| Merged band | Phrases in band | Phrase share | At 10 items/day this means… | Block (5 items) |
|---|---|---|---|---|
| Top 1,000 | 32 | ~3% | about 1 phrase every 3 days | 5 words (sometimes 4 + 1) |
| 1,001–2,000 | 85 | ~8% | about 1 phrase a day | 5 words or 4 + 1 |
| 2,001–5,000 | ~390 (≈ 130 per 1,000) | ~13% | ~1–2 phrases a day | **4 + 1** |
| Plus phrasal verbs, spoken formulas, L1 traps (not all in PHRASE) | — | pushes B1+ toward ~15–20% | ~2 a day | **4 + 1**, sometimes 3 + 2 |

**Recommendation:**
- **No fixed quota.** The ranking decides, inside these guardrails:
  1. **at most 2 phrases per 5-item block**, so a block never feels like an idiom drill;
  2. from the 1,001–2,000 band upward, **at least 1 phrase per day**, so phrases never disappear;
  3. **social and spoken formulas** (*thank you*, *how are you*, *excuse me*) are introduced in the first week for A1 learners regardless of rank, because they're needed for the companion and chat steps.
- So for a typical B1 learner, **David's "4 words + 1 phrase" is what the data produces**. For a beginner it's mostly 5 words, with a phrase every few days.

## 12.5 How interests change what counts as "most common"

**David decided:** most-common depends on the texts the user is interested in.

**Topic frequency profiles.** Every human-written corpus text is tagged with topics ([06 §6.7](06-content-pipeline.md#67-matching-content-to-users)). For each topic *t*, we compute each item's frequency in that topic, f_t(i), and its **keyness**: how much more frequent it is in the topic than in general English.

**Personal frequency:**

```
f_u(i) = α · f_general(i)  +  (1 − α) · Σ_t  w_u,t · f_t(i)
```

- **w_u,t** = the user's interest weight for topic *t* (from onboarding, feedback and behaviour; [13](13-interests-and-feedback.md)), normalised to sum to 1 over their top topics.
- **α = 0.7** by default: 70% general English, 30% their interests. General English stays dominant because every text, whatever the topic, is built on the core vocabulary.
- **f_general** is itself a goal-dependent mix: Conversation goal → 60% spoken / 40% written; Study → more written and academic.

**What changes in practice (illustrative):**
- A technology fan sees *device*, *update*, *launch*, *data* and *figure out* move up.
- A cooking fan gets *recipe*, *boil*, *slice*, *ingredient* and *cut down on* earlier.
- An entrepreneur gets *revenue*, *customer*, *invest*, *come up with*.

**Guardrails.**
- **Core protection:** at most **1 in 5** of a learner's new items may be one that the interest boost pulled ahead of a more frequent general item. Interests flavour the list; they don't replace it.
- **Niche words** (outside the general top ~8,000, e.g. *blockchain*, *sous-vide*) qualify only if:
  - (a) they're in the user's top-2 topics, **and**
  - (b) they're frequent *within* that topic (top 500 by keyness × frequency), **and**
  - (c) the user expressed the interest explicitly (a bubble or the free-text box), not just through behaviour.
- **Changing interests:** weights decay toward a baseline over time ([13](13-interests-and-feedback.md)). The ranking is recomputed nightly, so the list follows the learner.

## 12.6 Data model (lexicon side)

```text
lexicon_releases(version, created_at, source_snapshots_json, notes)
corpora(id, name, register[spoken|written|academic|news|fiction], topic_id, licence,
        token_count, snapshot_date, human_written bool)              -- frequency jobs read human_written only
items(id, kind[word|phrase], headword, pos, phrase_type, is_function_word, status[active|candidate|deprecated],
      merged_into, cefr, general_zipf, spoken_zipf, written_zipf, dispersion, opacity, band, created_in_version)
item_forms(item_id, form, form_type[inflection|spelling_variant])
phrase_patterns(item_id, tokens_json, separable bool, max_gap)        -- e.g. give + (0–3 tokens) + up/PART
item_components(phrase_id, word_item_id, position)                    -- eligibility rule, Vault links
senses(id, item_id, rank, definition_en, example_en, share)
item_topic_freq(item_id, topic_id, per_million, keyness, lexicon_version)
item_tags(item_id, dimension[function|register|type|goal_list], value)
item_l1(item_id, lang, gloss_sense_json, cognate[none|cognate|false_friend], l1_trap bool, note, irt_difficulty)
item_relations(item_id, related_id, type[family|confusable|synonym])
item_live_stats(item_id, l1, lookups_per_10k_tokens, first_check_accuracy, median_days_to_deep,
                report_count, last_computed)                           -- from user data, nightly
lexicon_candidates(id, text, kind, evidence_json, source[drift|user_lookups|editor|request], status, reviewer)
```

- **Item IDs are permanent.** Items are never deleted, only deprecated or `merged_into` another, because every encounter event in [04 §4.7](04-review-model.md#47-data-model-for-encounters) points at an item ID.
- **On the phone:** the app ships only the active items plus the glosses for the user's native language (~5k rows, a few MB in SQLite). Topic frequencies stay on the server. The nightly planner sends each user their next ~70-item candidate list (the ±7-day pool).

## 12.7 Maintenance: keeping the database right over time

| Job | Trigger | What it does | Human step |
|---|---|---|---|
| **Live stats** | Nightly | Per item and native language: lookup rate, first-check accuracy, days to Deep, reports | None |
| **Difficulty recalibration** | Each lexicon release | Items whose real difficulty differs from their band (e.g. a cognate that's trivially easy for Portuguese speakers) get per-native-language IRT offsets ([14](14-gaps-and-test-plans.md)) | Spot-check outliers |
| **Gloss queue** | Report count ≥ 3, or a high "looked up but still failed" rate | Puts the gloss or sense back into review | Native-speaker reviewer fixes |
| **New-item detection (drift)** | Each corpus ingest | Lemmas and n-grams that are frequent in *recent human-written* corpus texts but missing from the lexicon (new tech words, new expressions) → candidate queue | Editor approves, sets type/tags |
| **User-driven candidates** | Nightly | Words many users look up that aren't items (e.g. *vibe*), and phrases users type in "I want to read about…" boxes | Editor approves |
| **Topic profile refresh** | Each corpus ingest | Recompute `item_topic_freq` and keyness for topics whose corpus grew | None |
| **Frequency refresh** | Each lexicon release | Recount from human-written sources only; compare ranks; flag big jumps | Editor reviews jumps > 1,000 ranks |
| **Release** | When the queue has enough approved changes | New `lexicon_version`; users' item states are untouched; the planner uses the new ranks from the next night | Release notes |

**Internal tool needed (gap in the earlier plan):** a small **lexicon admin console**: search items, edit tags and glosses, approve candidates, merge duplicates, see live stats. Without it, every fix needs an engineer. It's a modest web app on the same database, behind staff login.

---

## Sources
- [S1] wordfreq SUNSET note (Robyn Speer, Sept 2024) — https://github.com/rspeer/wordfreq/blob/master/SUNSET.md
- [S2] van Heuven, Mandera, Keuleers & Brysbaert (2014), "SUBTLEX-UK: a new and improved word frequency database for British English", *QJEP* 67(6) — introduces the Zipf scale
- [S3] Martinez & Schmitt (2012), "A Phrasal Expressions List", *Applied Linguistics* 33(3), 299–320 — https://www.lextutor.ca/tests/pvst/martinez_schmitt_2012.pdf ; list with integrated ranks — https://lextutor.ca/freq/lists_download/phrase_list_martinez.htm
- [S4] Gardner & Davies (2007), "Pointing out frequent phrasal verbs: a corpus-based analysis", *TESOL Quarterly* 41(2), 339–359 — https://www.mark-davies.org/articles/davies_31.pdf
- NGSL — https://www.newgeneralservicelist.com/ · SUBTLEX-US — https://www.ugent.be/pp/experimentele-psychologie/en/research/documents/subtlexus · CEFR-J/Octanove — https://github.com/openlanguageprofiles/olp-en-cefrj · PHaVE — https://www.norbertschmitt.co.uk/vocabulary-resources
