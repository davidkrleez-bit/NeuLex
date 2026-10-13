# 00 · How it all works, in plain language

[← Back to plan index](../neulang-plan.md)

This is the walkthrough of the whole system: what happens to a word from the moment it enters the database to the moment the learner has it for good. The other sections hold the detail and the evidence. This one shows **how the pieces connect**. Numbers marked *illustrative* are examples of how the rules play out, not extra rules.

Status tags follow the rest of the plan. Where this page summarises a recommendation, the linked section holds the **"Recommendation — needs David's check"** tag.

---

## 0.1 The big picture in one diagram

```
 HUMAN-WRITTEN ENGLISH                    THE USER
 (subtitles, news archives,               (native language, goal, interests,
  Wikipedia, books…)                       placement answers, feedback, taps)
        │                                          │
        ▼                                          ▼
 ┌──────────────────┐   ranks   ┌──────────────────────────────┐
 │ 1. LEXICON        │─────────▶│ 2. PERSONAL WORD LIST          │
 │ ~4,000 words +    │          │ "the most useful words *you*   │
 │ ~700 phrases,     │◀─ new ───│  don't know yet", re-ranked    │
 │ frequency per     │  words   │  every night                   │
 │ topic, glosses    │          └───────────────┬───────────────┘
 └──────────────────┘                          │ next ~70 items (the ±7-day pool)
                                               ▼
 ┌──────────────────────────────────────────────────────────────┐
 │ 3. LESSON BLOCK (5 items, ~15 min)                            │
 │ triage → anchor → STORY (each word ×3) → cloze check →        │
 │ use it (optional) → recap card → speed round → "was this      │
 │ interesting?"                                                 │
 └───────────────┬──────────────────────────────┬───────────────┘
                 │ every encounter is logged     │ feedback
                 ▼                               ▼
 ┌──────────────────────────────┐   ┌──────────────────────────┐
 │ 4. MEMORY MODEL               │   │ 5. INTEREST MODEL         │
 │ per word: state, encounter    │   │ topic & format weights,   │
 │ points, FSRS next-review date │   │ custom interests          │
 └───────────────┬──────────────┘   └────────────┬─────────────┘
                 │ due words, fading words        │ topics
                 ▼                                ▼
 ┌──────────────────────────────────────────────────────────────┐
 │ 6. EVERYTHING ELSE THE USER DOES                              │
 │ free reading · AI stories · listening practice · companion    │
 │ chat · Vault — all fed by 4 and 5, all logging back into 4    │
 └──────────────────────────────────────────────────────────────┘
```

**Where it runs** ([09](09-architecture.md)):
- **On the phone:** the app, a local copy of the user's words and lessons, and the scheduler that decides what's due. Lessons, reviews and reading work offline.
- **On the server** (Supabase): the master lexicon, everyone's word states and encounter history (synced with PowerSync), and the AI gateway that every AI call goes through.
- **Overnight jobs:**
  - re-rank each user's word list;
  - pick tomorrow's words and write tomorrow's stories (at half price through batch APIs);
  - render audio;
  - ingest and tag new texts;
  - refresh word statistics.

## 0.2 Step 1 — the database of words and phrases

Full detail: [12](12-vocabulary-database.md).

1. **We count words in real, human-written English.**
   - Spoken English comes from film/TV subtitles; written English from news, Wikipedia and books.
   - We also count words in our own text collection, split by topic.
   - AI-written text is **never** counted. The best-known free frequency list stopped updating in 2024 because AI text had polluted the web.
2. **We check the result against an expert list** (the New General Service List) and remove ~300 grammar words (*the*, *of*, *is*…). They're everywhere, so they aren't study items.
3. **Phrases are added as their own items**: *give up*, *at least*, *by the way*, *make a decision*.
   - They come from published phrase lists, curated spoken expressions, academic phrases, and **"trap" phrases** for each native language (Spanish speakers say "make a question" because Spanish uses *hacer una pregunta*).
   - Each phrase is categorised by **type** (phrasal verb, fixed expression, spoken formula…), **function** (giving opinions, time, contrast…), **register** (spoken, written, academic), **CEFR level**, **how guessable it is** from its words, and whether it's a **trap** for this native language.
4. **Words and phrases are ranked on one scale:** *how much more of the English you read will you understand after learning this?* *Have to* is as common as a top-110 word, so it's taught as early as one. *At the moment* sits near word #2,000.
5. **The mix this produces** (David asked "5 words and 1 phrase, or 4 and 1?"):
   - mostly 5 words at beginner level, with a phrase every few days;
   - **about 4 words + 1 phrase per block from intermediate (B1) up**, because phrases make up a growing share of the next most common items;
   - never more than 2 phrases in a block.
6. **The database is kept up to date** with nightly statistics (which words people look up, which glosses get reported), new-word detection from fresh human-written texts, and editor-approved releases. Item IDs never change, so nobody's history breaks.

## 0.3 Step 2 — placement: what the user already knows

Full detail: [03](03-placement-test.md).

- About **5 minutes, 35–45 questions**. Each question is a real English word in a short sentence, with **4 possible translations in the user's language plus "I don't know"**.
- **The words keep getting harder** until the user starts missing them, then the test narrows in. **No fake words** (David decided).
- **Lucky guesses are caught** by asking a second, reverse-format question in the same band whenever someone gets a word right that's above their level.
- It also checks **phrases and second meanings** separately, because knowing *give* and *up* doesn't mean knowing *give up*.
- **Output:** a probability that the user knows each of the ~4,700 items, a vocabulary size ("about 2,300, give or take 300"), and the gaps to start with.

## 0.4 Step 3 — the personal word list: how interests change it

Full detail: [12 §12.4–12.5](12-vocabulary-database.md#124-ranking-words-and-phrases-on-one-scale), [13](13-interests-and-feedback.md).

Every night, each user's list is re-ranked using:

> **usefulness = how common it is in the kind of English *you* read × how hard it is to guess × how likely you don't know it yet**

- **"The kind of English you read"** is 70% general English and 30% your interest topics. A word ranked 2,600th overall but 300th in business writing comes earlier for someone who loves business texts.
- **Core protection:** at most 1 in 5 new items can jump the queue because of interests. Everyday English always comes first.
- **Words you looked up in reading** get a boost, so things you tripped over come back as lessons.
- The top ~70 items form the **±7-day pool**. Tomorrow's story picks a topic-coherent handful from it.

## 0.5 Step 4 — a lesson block, start to finish

Full detail: [05](05-lessons-and-exercises.md). At the default of 10 new items a day there are 2 blocks of about 15 minutes, which can be done at different times.

| Step | What the user does | What the system records |
|---|---|---|
| **Triage** | Swipes through the block's 5 items: *Learn* / *Already know* (followed by a 2-second check) | "Already know" + pass → the item is skipped and the next one from the pool is pulled in |
| **Anchor** | Sees each item with a picture or a short native-language gloss and an example | The word's memory clock starts |
| **Story** | Reads a ~1,000-word story (B1+) on one of their interests, where **each new item appears at least 3 times**, plus 2–3 older words that are starting to fade | One "read" encounter per appearance (counted only if the paragraph was on screen long enough to read) |
| **Cloze check** | Fills in 5–8 blanks in real sentences (human-written where possible) | **The main way a word moves up**: an answer key decides, not the AI |
| **Use it** (optional, on by default from A2) | One of: an opinion question, describing a picture, or a short AI chat | The AI can only *flag* problems. It can never move a word up |
| **Recap card** | For each word they used: "Nailed it / Not sure / Didn't know it" | The learner's own rating is the only way writing moves a word up, and only provisionally until the next keyed check |
| **Speed round** | ~4 minutes of fast cards: due reviews, then today's items again | Answer-keyed reviews update the schedule. Leftovers wait for tomorrow and are never shown as a scary count |
| **"Was this interesting?"** | 👍 / 😐 / 👎. 👎 opens topic bubbles, reasons and a text box | Interest weights; difficulty complaints go to the level model ([13 §13.4](13-interests-and-feedback.md#134-the-end-of-text-review-card-every-text)) |

## 0.6 Step 5 — how stories are created

Full detail: [06 §6.4](06-content-pipeline.md#64-pure-ai-stories), [06 §6.8](06-content-pipeline.md#68-qa-and-safety-chain-for-generated-content).

1. **Plan (overnight).** The planner chooses a **topic** from the user's interests (sometimes a nearby topic, to explore), then picks the **5 pool items that fit it best**, then adds 2–3 fading review words. If too few words fit the topic, the words win and the topic changes.
2. **Budget.** It sets the level, the length (≈ 200 words per new item, so new words make up at most ~1.5% of the text) and the rule that everything else must be words the user already knows (≥ 98% coverage).
3. **Write.** The AI writes an outline, then the story, in a rotating genre (mystery, workplace drama, sports, slice of life…), avoiding a list of tired AI clichés.
4. **Check (no AI judgement).** A validator counts every target (≥ 3 times), the density, the coverage, and any off-list words. It also checks each target is used in the intended meaning.
5. **Fix or fall back.** If it fails, the AI rewrites with the problems listed (up to 2 tries). If it still fails, the block uses a real human-written text that contains the words instead.
6. **Add the rest:** comprehension questions, cloze sentences, audio, the "AI-written" label, and safety moderation.
7. **Reuse.** Popular shared stories (by level and topic) are written once and reused, which is cheaper and lets a human review them. Series with recurring characters bring readers back.
8. **The first lesson** is written while the user takes the placement test, with a pre-written fallback so the first session never waits.
9. **About 2 days a week**, the story slot uses a **real text excerpt** instead, and the block's words are taken *from* that text.

## 0.7 Step 6 — what spacing looks like for one word

Full detail: [04](04-review-model.md). Two clocks run for every word:
- **The memory clock (FSRS)** decides when the next *check* is due. Only answer-keyed checks and the learner's own recap rating move it.
- **Encounter points** count every meeting with the word: reading 0.5, a multiple-choice check 1.0, a cloze or speed-round recall 1.5, used in a lesson writing task 2.0 (companion chat 1.0) when confirmed. They show progress and decide when a word is *Locked in*.

**One word, illustrative** (a B1 learner meets *reluctant*; every check answered correctly):

| When | What happens | Memory clock | Encounter points (running total) |
|---|---|---|---|
| **Day 0**, block 1 | Anchor; story ×3; cloze ✓; speed round ✓; used in the opinion answer + "Nailed it" | Short steps inside the session (≈ 1 min, ≈ 10 min), then next check in **≈ 2 days** | ≈ 5 |
| Day 1 | Appears once in a free-reading article | Read without a lookup: a small, capped nudge | ≈ 6 |
| **Day 2** | Speed-round card ✓ | Next check in **≈ 6 days** | ≈ 7.5 |
| Day 4 | Appears twice in another story as a review word | Small nudge | ≈ 8.5 |
| **Day 8** | Cloze ✓ | Next check in **≈ 2–3 weeks** | ≈ 10 |
| Day 12 | Used in the companion chat; learner confirms it on the recap card | Provisional until the next keyed check | ≈ 11 |
| **Day ~25** | Speed-round card ✓ → **Locked in** (12+ points, 3+ keyed successes on different days, seen in 2+ texts, memory predicted to last 3+ weeks) | Routine checks stop | ≈ 12.5 |
| Day 25 → | Each time the word turns up in reading or chat, the 60-day timer resets | — | grows |
| **60 days without meeting it** | One quick resurfacing card | Pass → *Mastered*, next gap 120 days, then 240 | — |

**What changes the picture:**
- **Same-day repeats count less:** the 2nd read of the day is worth half, the 3rd a quarter, and so on. The first meeting of the day *in a new text* gets a bonus.
- **A wrong answer** sends the word back to short steps, and the gaps shrink.
- **Looking the word up** while reading:
  - for a word still being learned, it's a warning sign: the word gets a slot in that day's speed round, and that read earns no nudge;
  - for a word marked *Locked in* or *Already knew*, it counts as forgotten, and the word goes back to *Getting stronger* (David's re-tap rule).
- **The AI never moves a word up or down.** It only flags.
- **Listening** trains the ear and is shown as its own badge. It barely affects the schedule, because evidence shows words aren't learned well by listening alone.
- **Missed days** never create a pile-up. The speed round has a time budget, reviews are spread across days, and new words slow down automatically if reviews fall behind.

## 0.8 Step 7 — interests: how we ask, and what changes

Full detail: [13](13-interests-and-feedback.md).

- **At sign-up:** pick at least 3 topic bubbles from 8 groups (double-tap for a favourite), optionally pick formats (news, stories, how-to…), and optionally type anything specific ("Formula 1", "running a coffee shop"). The app shows back what it understood as editable chips.
- **After every text** (David decided): 👍 / 😐 / 👎.
  - 👎 opens **topic bubbles** (News · Self-help · Entrepreneurship & business · Environment · Technology · more…), **reason chips** (boring, too hard, too easy, too long, felt AI-written…) and a **big text box**.
  - It never blocks the lesson, and it shrinks to one row if the user keeps skipping.
- **What changes, and how fast:**
  - **reading suggestions:** immediately;
  - **story topics:** from the next block, and a tapped topic is guaranteed in the next 3 texts;
  - **which words are taught:** gradually over about a week, because the next week's words are already planned.
- **Showing it's working:** "Because you asked for *business*" labels, "why this word?" chips, an interests page with each request's status, and a weekly recap line.

## 0.9 Step 8 — what the user sees about their progress

Full detail: [01](01-scope-and-journey.md), [14 §14.9](14-gaps-and-test-plans.md#149-test-plan-what-users-see-analytics-screens). **Recommendation — needs David's check.**

**Three headline numbers (Profile and the weekly recap):**
1. **Words you know** — "≈ 2,460 (give or take 300)". It starts at the placement estimate and grows. Underneath: "**Learned with NeuLang: 160** · on pace for 3,400 this year".
2. **Memory strength** — "You remembered **91%** of your checks this month". This comes from answer-keyed checks only.
3. **Streak** — days in a row with at least one block or 2-minute session, with one free "freeze" a week.

**Detail screens:**
- **Word journey bar:** New · Learning · Getting stronger · Locked in · Mastered, with counts, plus "Already knew: 2,300".
- **Band progress:** "Most common 1,000: 96% · 1,001–2,000: 71% · 2,001–3,000: 38%", with phrases shown as their own bar.
- **Your topics:** "Business: 34 words · Technology: 21 words".
- **Reading and listening:** texts read, words read, minutes of listening practice, and "your words met in reading this week".
- **Each word in the Vault:** its timeline (every dated meeting, with a link to the text), the next check date, what's left before *Locked in*, and the ear badge.
- **Coverage %** ("you understand ~93% of everyday English") **only after 6 months**, when the change is big enough to be real.
- **Weekly recap:** words learned, words locked in, texts read, the topics behind them, and one suggestion ("3 words are fading — here's a 2-minute story").

**What we track internally** (not shown to users):
- funnels and retention;
- the brief's validation metrics (sustained intake, drop-off by pace, AI false-accept, content errors, measured encounters per word);
- audit-probe calibration;
- content ratings;
- AI cost per user.

All are listed in [10](10-metrics-risks-roadmap.md) and [14](14-gaps-and-test-plans.md).

## 0.10 Where the plan was weakest, and the fixes

The full list is in [14 §14.1](14-gaps-and-test-plans.md#141-gaps-where-users-may-feel-lost-or-the-system-doesnt-connect). The ones that matter most:
- **"2,300 known" vs "0 learned"** looked contradictory. Fix: one growing headline number.
- **State names were jargon.** Fix: plain labels (*Getting stronger*, *Locked in*).
- **The reader broke phrases** (*gave up* glossed as "gave" + "up"). Fix: a phrase-aware reader.
- **The companion didn't know what you're learning.** Fix: give it the words to weave in, add "Chat about this story", and offer a recap card at the end.
- **Feedback had no visible effect.** Fix: "Because you asked for…" labels and request status.
- **No ground truth.** Fix: one random audit question a day, plus pilot tests against an established vocabulary test.
