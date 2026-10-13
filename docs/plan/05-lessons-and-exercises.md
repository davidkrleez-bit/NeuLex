# 05 · Lesson composition, rotation and exercise specs

[← Back to plan index](../neulang-plan.md)

**David decided:**
- The lesson is a **multimodal mix that doesn't overwhelm**.
- Inputs: a story, *sometimes* a podcast, *sometimes* a short YouTube section.
- Exercises: text exercises; an **opinion prompt** shaped so the answer uses the target words; a **picture** the user describes using the word; a short **news / article / story excerpt**.
- It ends with a **speed round** of flashcards.

**David decided (planning brief, 2026-10-13):**
- **The story is the primary vocabulary engine.**
- **Fill-in-the-blank (cloze)** is its own modality (cued recall).
- The **podcast is listening training, explicitly not a vocabulary channel**.
- **Production is opt-out, not mandatory.**
- **AI chat is optional and clearly labelled.** The paying segment is hostile to mandatory AI chat.
- Each target must be **repeated within one coherent text**.
- **Content scales by count:** more words means more stories, not longer ones.
- Production must be **capped per session and rotated**.

**Superseded.** David's earlier sketch ("10 flashcards, one 500-word story, 5 Textercises in ~8 minutes") didn't fit 8 minutes. Under the brief's intake ladder ([04 D4.8](04-review-model.md#d48-intake-ladder--recommendation--needs-davids-check-adopted-from-davids-planning-brief-replaces-the-earlier-5-new-wordsday)), the unit is now a **5-word lesson block**: 1 block a day on the on-ramp and low-energy settings, 2 blocks at the default 10/day. The structure below is a **Recommendation — needs David's check**.

---

## 5.1 Lesson skeleton

### D5.1 Lesson block: fixed slots, rotating content — **Recommendation — needs David's check**

One **block** introduces 5 items (new words from the ±7-day pool, chosen to be topic-coherent).

| # | Slot | Content | Time (my estimate) | Always? |
|---|---|---|---|---|
| 1 | **Warm-up** | Triage deck (L1) → anchor for each of the block's 5 items (L2) | 2–3 min | Yes |
| 2 | **Story** (primary vocabulary engine) | Story (L3) **or**, about 2 days a week, a real-text excerpt (L3b) that meets the same repetition rules | 5–8 min | Yes |
| 3 | **Cloze check** | 5–8 answer-keyed cloze items (L3c) on the block's items + due words. **The main promotion source** | 1.5–2 min | Yes |
| 4 | **Use it** (production, opt-out) | **One** of: opinion prompt (L6) · picture description (L7) · Textercise AI chat (L5, optional and labelled) | 2–3 min | Default on from A2; off at A1 (opt-in) |
| 5 | **Speed round** | Time-capped (~4 min budget per block, [04 §4.8](04-review-model.md#48-load-control-and-intake-avoiding-review-pile-ups)) | ≤ 4 min | Yes |
| 6 | **Wrap-up** | Recap card for productions (learner self-rating, [04 §4.2b](04-review-model.md#42b-post-production-recap-card-learner-authority)) → "Was this interesting?" card (L9, after every text) → complete (L10) | 0.5–1 min | Yes |
| + | **Listening training** (separate, optional) | Mini-podcast (L4), framed as *listening practice*, not as a way to learn new words. Built from words already in Reviewing/Deep | 2–3 min | Optional, from the Today screen |

**Daily time by intake (my estimate, B1 learner):**

| Intake | Blocks | With production | Production opted out |
|---|---|---|---|
| 5/day (on-ramp, low-energy) | 1 | ≈ 15–18 min | ≈ 12–15 min |
| **10/day (default)** | 2 (can be done at different times) | **≈ 30–35 min** | ≈ 25 min |
| 15/day (ceiling) | 3 | ≈ 45–50 min | ≈ 38 min |

The brief's model gave ~56 minutes at 10/day because it included 16 minutes of video and 4 Textercises a day. **This is linear by design**, so the time is a real cost of the 10/day headline. David must accept it explicitly (it's in the decisions list).

**Overwhelm rules.**
- One block = one story + at most one production task.
- **Production is capped at one task per block and rotated** (brief).
- David asked explicitly for lessons not to overwhelm. Splitting into blocks keeps each sitting at about 15 minutes, even at 10/day.

**Encounters per new item in its first block** (enforced by the planner and **measured**, not assumed):
- anchor ×1;
- **story ×3** (the repetition rule in L3);
- cloze ×1;
- the speed round's second exposure ×1;
- production ×0–1.

That's ≈ 6–7 in the first block. Keyed reviews and review-word appearances in later stories during the next 2–4 weeks bring each item to the **8–12 encounter design target** (Webb 2007: ~10 for sizeable gains, more for full knowledge [S7]).

### D5.2 Rotation rules — **Recommendation — needs David's check**
- **Story slot:**
  - about 5 AI/adapted stories to 2 real-text excerpts a week;
  - never the same source type 3 blocks in a row;
  - listening is **not** in this rotation; it's a separate optional training slot.
- **Use it:**
  - weekly mix of about opinion ×3, picture ×2, chat ×2. Chat is weighted lowest because it's the most AI-dependent and the least wanted by the paying segment (brief §7);
  - **picture only if ≥2 of today's words are imageable** (concreteness rating ≥ 3.5/5 on Brysbaert concreteness norms [S1]; *cheating* works as a scene, *however* doesn't).
- **Novelty cap:** at most 4 different exercise types in one lesson, counting triage and speed round.
- **Opt-out, not mandatory:**
  - a settings toggle "**Writing practice**: on / off" (default on from A2; off at A1, since early production is widely seen as wasted time at beginner levels);
  - a separate toggle "**AI chat exercises**: on / off";
  - a "Skip today" link on every production step.

  Skipping costs nothing except the encounter points; items still reach Deep via the 2-texts route ([04 D4.3](04-review-model.md#d43-deep-criteria--recommendation--needs-davids-check)). Skip rates are tracked per type and per user, and the rotation adapts (a user who always skips pictures gets fewer).
- **Pre-generation:** tomorrow's lesson is generated **overnight** (in a batch, at 50% LLM cost; see [08](08-unit-economics.md)) and downloaded. Lessons start instantly and work offline. Only grading and chat replies need the network. Offline fallback: a local rubric (target word present, lemmatised, plus a length check) and a deferred LLM regrade when back online.

## 5.2 Exercise specs

### L1 · Triage deck (David's original idea)
- **Shows** 5–8 candidate words one at a time: the word, a short English example sentence and audio.
- **Actions** (David's): ✋ *Don't know* · ➕ *Learn* · ✅ *Already know*.
  - *Don't know* and *Learn* both add the word to today's 5.
  - *Don't know* also starts it with a lower prior.
- **"Already know"** triggers one quick MCQ check (sentence + 4 native-language options). Pass → Known(probable). Fail → added to today's words, with a gentle "Let's make sure."
- Deck ends when 5 words are chosen. If too many are "already known", it pulls more candidates (max 15 cards).
- **Events:** `anchor` is not logged here. A correct check logs `recognition`.

### L2 · Anchor (pick 1 of 4 images, David's original idea)
- For each word: the gloss in the native language, an example sentence and audio.
- **Imageable words:** show 4 images. The user picks the one that fits the word *for them*, and it becomes the word's personal image in the Vault (`chosen_image_id`).
- **Non-imageable words:** an example sentence plus "Which sentence uses *however* correctly?" (2 options).
- Pictures help only when learners don't become overconfident from them. Carpenter & Olson found pictures can *hurt* foreign-word learning unless retrieval practice follows [S2]. So the anchor is always followed by a retrieval in the speed round.
- **Events:** `anchor` (0.5).

### L3 · Story / excerpt in the chip reader
- **Repetition rule (brief: "guarantee repetition of each target inside one coherent text"):** each new target appears **≥ 3 times**, in different sentences spread through the story, plus **2–3 review words** with low retrievability.
- **Density ceiling:** new-target tokens ≤ **1.5%** of the story. Above that, a story turns into "a word list with prose" (brief). The ceiling also matches the 98%-coverage goal for unknown words.
- **Length follows from the targets:** length ≈ max(level minimum, 200 words × new targets in the story).
  - **B1+:** 5 new targets → **≈ 1,000 words**. The brief's reference point is ~5 targets per ~1,400 words, with a hard ceiling of ~7.
  - **A1–A2:** **3 new targets → ≈ 600 words**. The block's other 2 items get their story appearances in the next block's story, within the ±7-day pool.
  - **More words = more stories, not longer ones** (brief), which is why 10/day = 2 blocks.
- **Coverage target:** **≥ 98%** known tokens, counting new targets as unknown (see [02](02-english-lexicon-and-languages.md)).
- **Instrumentation:** the lexical validator *records* the actual count of each target in every delivered story, and the encounter log records what was actually read (dwell check). The "3 appearances per story" assumption is **measured from day one**, not assumed (brief §11).
- **Chip reader behaviour (David's original):**
  - tap a word → native-language gloss popup (R2), the word turns yellow and is added to the Vault;
  - **bold** = currently learning;
  - a small counter badge shows that word's encounter count.
- **Comprehension:** 2 quick questions afterwards (MCQ), which keeps reading honest. They give no word credit unless the question tests a target word.
- **Audio:** a play button uses cached TTS (see [08](08-unit-economics.md)).
- **Events:** `passive_read` per tracked item on dwell; `lookup` on tap.

### L3c · Cloze check (David decided modality: fill in the blank)
- **What:** 5–8 sentences, each with one blank, after the story. Answer-keyed, so this is the **main source of interval promotions** ([04 §4.0](04-review-model.md#40-promotion-authority-the-rule-every-other-part-obeys)).
- **Sentence source, in order of preference:**
  1. **Human-written sentences from Tatoeba** (CC BY 2.0 FR: commercial use allowed with author attribution and a licence notice). Many come with Spanish/Portuguese translations to use as a hint line. Native-speaker status in Tatoeba is self-reported, so sample-check quality [S8];
  2. sentences from public-domain or CC texts in the corpus;
  3. AI-generated sentences that passed the content QA chain.

  Preferring human-written sentences answers the brief's "authenticity" objection, the same way Clozemaster (which also uses Tatoeba) does.
- **Answer key:** the target lemma plus its allowed inflections from the lexicon. The blank's expected form is fixed per sentence.
  - Accepted: an exact match, or a near miss within edit distance 1 when the word has > 5 letters (graded Hard).
  - Wrong form of the right lemma = Hard.
  - Anything else = Again.
- **Formats by level:** A1–A2 pick from 4 options (recognition); B1+ type the word, with a first-letter hint available (recall; using the hint caps the rating at Hard).
- **Events:** `recognition` or `recall`, `authority = answer_key`.

### L4 · Listening training (mini-podcast) — separate, optional
- **Framing (brief):** "Listening practice" — it trains your ear on words you already know. Never sold as a way to learn new words. Reading doesn't transfer automatically to listening, so this is a separate skill track.
- **MVP format:** a 60–120 second AI-scripted, 2-voice conversation (host + guest) on one of the user's interests, rendered with TTS. Built mostly from items in Reviewing/Deep, with ≤ 2% unknown tokens. Speaking rate by level (A2 ~110 wpm, B2 ~150 wpm).
- **Coverage note:** about 95% coverage is enough for adequate listening comprehension. For informal spoken English that's roughly 2,000–3,000 word families, vs 6,000–7,000 families for 98% (van Zeeland & Schmitt 2013, *Applied Linguistics* 34(4)) [S9]. So listening becomes usable much earlier than unassisted reading. That's a good motivational message for the B1 range.
- **Controls:** play/pause, 10-second rewind, speed 0.75×/1×, transcript toggle (off by default for B1+).
- **After:** 2 comprehension questions + "Which of these did you hear?" (word recognition, graded).
- **Archive:** VOA Learning English public-domain audio clips when the topic matches (see [06](06-content-pipeline.md)).
- **Events:** `passive_listen` (transcript off) or `passive_listen_read`; recognition events from questions.

### L5 · Textercise chat (David's original idea) — optional, labelled "AI chat"
- **Label and opt-out:** the step header says "AI chat practice" with an ⓘ disclosure. It's off if the user disabled AI chat exercises, in which case the slot rotates to opinion or picture (both work without a chat partner). Brief evidence: the paying segment is hostile to mandatory AI chat.
- **Setup:** an AI character with a persona tied to the story's topic. **3 target-word chips** shown above the keyboard. The AI is biased toward in-study words.
- **Goal / gate:** use all 3 chips within **6–10 turns**. A chip ticks when the **deterministic gate** passes ([04 D4.7](04-review-model.md#d47-textercise-gate-appeared-or-used-correctly--recommendation--needs-davids-check)):
  - the lemma appears (inflections allowed; typos within edit distance 1–2 for words > 5 letters);
  - in a learner-written sentence of ≥ 4 words;
  - as the expected part of speech;
  - not copied from the bot's turn;
  - not a bare list.
- **AI judge:** checks sense and usage, and **may only flag**. A flagged chip shows ⚠ with the explanation and a "try again" option. The learner can still finish, but the flagged word won't promote.
- **Feedback:** never a bare "✓ Correct!". Show "✓ *cheat* — matches: *to act dishonestly to gain an advantage*", or "⚠ Possibly: *cheat on a test*, not *cheat a test*." The full correction appears in an expandable chip.
- **AI turn rules:** the bot steers toward unused chips ("So what would *you* do if you saw someone cheating?"), stays at the user's level (≤ 3% vocabulary above level) and never uses the target word for the user.
- **Ends with** the recap card (learner self-rating per word, [04 §4.2b](04-review-model.md#42b-post-production-recap-card-learner-authority)).
- **Events:** `production` with outcome correct / flagged / uncertain / incorrect, `authority = learner` only after the recap rating.

### L6 · Opinion prompt (David decided format)
- **Prompt generator:** a question that **naturally invites the target words** (David's example: "Do you believe we are doing enough to help our environment?" for *environment*, *pollution*, *reduce*). The LLM writes 3 candidate prompts. A validator checks that an answer can naturally use ≥ 2 targets and that the topic is safe (no politics/religion flashpoints by default; a user setting allows "debate topics").
- **Input:** 1–4 sentences; minimum length by level (A2 ≥ 15 words, B2 ≥ 40). A word bank shows the targets.
- **Grading:** see §5.3. The output is a per-target verdict plus 1–2 improvement tips and a rewritten "more natural" version.
- **Events:** `production` per target used.

### L7 · Picture description (David decided format)
- **Image:** a scene that *shows* the word's meaning (David's example: a student copying another student's test = *cheating*). AI-generated, reviewed scene library (see [07](07-images.md)).
- **Task:** "Describe what's happening. Use: *cheat*, *notice*." 1–3 sentences.
- **Grading:** same as the opinion prompt. The judge also gets the image description (alt-text) so it can judge relevance.
- **Fallback:** if the image fails or the word isn't imageable → swap to an opinion prompt.
- **Events:** `production`.

### L3b · Excerpt (real text, David decided)
- 300–1,000 words from a licensed or public-domain source (see [06](06-content-pipeline.md)).
- **Sentence-mining mode:** a real text can't be rewritten to repeat targets 3×, so on excerpt days the block's new items are **mined from the excerpt**: the highest-priority unknown items (by frequency band and interest weight) that occur in it. Items appearing fewer than 3 times get their missing encounters from extra keyed cloze sentences (L3c) and the next AI story.
- **Shown as-is** with attribution (R4). If its coverage is below 95%, the reader pre-glosses the hardest 3–5 words.
- If the licence allows adaptation (CC BY, public domain), an "Easier version" toggle shows the simplified text.

### L8 · Speed round (David decided)
- 8–15 cards. Each card has a 6–8 second soft timer and shows a progress bar.
- **Card types** rotate:
  - English → native-language meaning (4 options);
  - native-language meaning → English (type it, with first-letter hint available);
  - gap-fill sentence;
  - audio → meaning.
- Order: learning-step items first, then reviews, then today's words, then resurfacing (see [04 §4.5](04-review-model.md#45-priority-of-items-in-a-lesson)).
- **Finish screen:** "12/14 — 2 words will come back tomorrow."
- **Events:** `recognition` or `recall`, with latency.

### L9 · "Was this interesting?" card (David decided, 2026-10-13: after every text)
- Shown after **every** text the learner reads or listens to: the lesson story or excerpt, free-reading articles, AI stories, explainers, mini-podcasts. Not after cloze, speed round or chat.
- One tap: **👍 Yes, more like this · 😐 It was OK · 👎 Not for me** (or skip).
- 👎 opens a sheet with:
  - **topic bubbles** (News · Self-help · Entrepreneurship & business · Environment · Technology · more…);
  - **reason chips** (boring topic, didn't like the story, too hard, too easy, too long, felt AI-written);
  - a **big text box**: "Tell us what you'd like to read about…".
- Never blocks completion. It shrinks to a one-row card after 5 skips in a row.
- "Too hard / too easy" go to the level model, not the interest model.
- Full spec, weight updates and the "you were heard" confirmations: [13](13-interests-and-feedback.md).

## 5.3 AI grading of free text

### D5.3 Grading approach — **Recommendation — needs David's check**

**Evidence.**
- Large models can grade learner writing near human level *with calibration examples*: GPT-4 rating short L2 texts on CEFR reached QWK 0.81, with accuracy varying by native language [S3].
- On TOEFL Junior writing, GPT-4 scored QWK 0.77–0.78 vs 0.82 between human raters [S4].
- The common acceptance threshold for automated scoring is QWK ≥ 0.70 [S5].
- NeuLang's grading task is much **narrower** than essay scoring. "Was *cheat* used correctly, in a sensible sense?" is easier and should be more reliable.

**Design.**
1. **Deterministic pre-checks.** Was the lemma present (lemmatiser + typo tolerance)? Is the message long enough? Is it in English (language ID)? Profanity / self-harm moderation.
2. **LLM judge** at temperature 0 with **structured JSON output**:
   ```json
   {"targets":[{"lemma":"cheat","used":true,"verdict":"correct|minor_error|wrong_sense|wrong_form|not_used","evidence":"…","fix":"…"}],
    "overall_tip":"…","natural_rewrite":"…","confidence":0.0}
   ```
   The prompt includes the target's sense definition, the user's level and native language, and 6–10 few-shot examples per exercise type.
3. **The AI verdict never maps to an FSRS rating** (David's hard constraint, [04 §4.0](04-review-model.md#40-promotion-authority-the-rule-every-other-part-obeys)). It only does two things:
   - **veto:** any verdict other than `correct` with confidence ≥ 0.8 → flagged → no promotion, and a keyed cloze is queued;
   - **explain:** the tip and the natural rewrite shown to the learner.

   The promotion itself comes only from the learner's recap rating, capped at Good and provisional until the next keyed review.
4. **Uncertainty resolves to no promotion.** Low confidence → shown as "Not sure — let's check this one later", never marked wrong, logged with 0 points.
5. **Appeal button** (L12): "I think my answer was right" → re-grade with a stronger model for the *feedback text only*. Appeals never change scheduling. Disagreements go to a review queue for prompt fixing and become new gold-set items.
6. **Feedback tone:** praise first, at most 1–2 corrections, explanations in the native language at A1–B1.

**Validation before launch.**
- Build a gold set of **~200 human-rated learner answers per exercise type per native language**: write answers in the style of real learners, including typical errors, and have 2 raters label them.
- Ship when the judge reaches **QWK ≥ 0.70** (target ≥ 0.80) on the verdict scale.
- The most important number is the **false-accept rate**: the share of truly wrong answers the AI fails to flag. It must be **≤ 5% per learner native language** (the brief's fail line). It matters most because a missed error is the only path by which an error reaches the learner's self-rating. Tune the flag threshold toward over-flagging, since a false flag costs only one promotion.
- Re-run the gold set on every prompt or model change; this is a regression test in CI. In production, re-measure the false-accept rate by native-speaker audit (see [10](10-metrics-risks-roadmap.md)).

## 5.4 Overwhelm safeguards (summary)
- One story and at most one production task per 5-item block; at most 4 exercise types per block.
- Intake ladder 5 → 10 (→ 15 gated) with time-based review caps and a backlog throttle (see [04 §4.8](04-review-model.md#48-load-control-and-intake-avoiding-review-pile-ups)).
- All production steps opt-out; AI chat optional and labelled.
- Resume at the exact step (David's original); if the lesson is left for > 24 hours, it restarts at the speed round, not the beginning.
- A progress bar with named steps ("Story → Chat → Speed round") so the user always knows how much is left.

---

## Sources
- [S1] Brysbaert, Warriner & Kuperman (2014), concreteness ratings for 40k English lemmas, *Behavior Research Methods* 46 — https://doi.org/10.3758/s13428-013-0403-5
- [S2] Carpenter & Olson (2012), "Are pictures good for learning new vocabulary in a foreign language? Only if you think they are not", *JEP: LMC* 38(1)
- [S3] Yancey et al. (2023), "Rating short L2 essays on the CEFR scale with GPT-4", BEA workshop — https://aclanthology.org/2023.bea-1.49/
- [S4] GPT-4 automated scoring of TOEFL Junior writing vs human raters (QWK 0.77–0.78 vs 0.82)
- [S5] Williamson, Xi & Breyer (2012), "A framework for evaluation and use of automated scoring", *Educational Measurement* 31(1) (QWK ≥ 0.70 threshold; also Ramineni & Williamson 2013)
- [S6] Yanagisawa & Webb, "Involvement Load Hypothesis Plus", *Language Learning* 71(2)
- [S7] Webb (2007), "The effects of repetition on vocabulary knowledge", *Applied Linguistics* 28(1), 46–65
- [S8] Tatoeba terms of use (CC BY 2.0 FR for text; audio licences vary) — https://tatoeba.org/en/terms_of_use ; downloads — https://tatoeba.org/en/downloads ; Clozemaster FAQ (uses Tatoeba) — https://www.clozemaster.com/faq
- [S9] van Zeeland & Schmitt (2013), "Lexical coverage in L1 and L2 listening comprehension: the same or different from reading comprehension?", *Applied Linguistics* 34(4), 457–479 — https://doi.org/10.1093/applin/ams074
- David's planning brief (2026-10-13) — [`internal/source/neulex-planning-brief.md`](../../internal/source/neulex-planning-brief.md)
