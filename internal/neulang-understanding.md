# NeuLang — Understanding of the Idea Doc

Source: [`neulang-source.md`](./neulang-source.md) (converted from `source/Anki_Analysis_for_Language_App_dc20.docx`; the scanned `source/neulang.pdf` is the same 17-page Gemini chat and adds nothing).

**Reading note.** The doc is an exported Gemini conversation titled "Anki Analysis for Language App". David's own words are only the italic *User prompt* lines. Everything after "Response:" is Gemini elaborating, and Gemini added a lot David never asked for. The export starts mid-conversation, so the "Anki analysis" the title refers to is **not in the doc**. Below, **[D]** marks David's stated intent and **[G]** marks Gemini's additions.

---

## 1. What the app is

A mobile-first (planned in Flutter) vocabulary-acquisition app. It teaches the **top ~1,000–3,000 high-frequency "content" words** of a target language at **5 new words per day**, through context instead of flashcard decks. Context means a personalized AI story, a short AI texting exercise, and a user-chosen image anchor per word. A light spaced-repetition (SRS) layer runs underneath. Progress is shown as **"% of everyday speech you understand"**, computed via Zipf's law, instead of card counts or XP. A free AI chat partner is always one tap away. The app was unnamed in the doc; "NeuLang" is David's name for it. The worked examples are all Spanish→English.

## 2. The problem, and for whom

**Who:** adult self-learners in the gap between beginner apps and native content. Examples: people who "finished the Duolingo tree" but can't follow real speech, Anki users who burned out, and intermediate learners on a plateau. Interest domains (business, travel, tech, slang) suggest working adults with practical goals.

**What he thinks is wrong with Anki.** This is inferred, since the doc never states it directly. It comes from the market-assumption section, the pitch copy, and the design choices.
- **Managing decks is a second job.** Building cards takes too long, and decks are complex and ugly.
- **Reviews pile up**, which leads to burnout and quitting. That is the reason for the hard 5/day intake cap.
- **Words are learned in isolation**, without real context, so they don't turn into comprehension.
- **Low-value items** like function words (por/para) have dozens of meanings, which causes "polysemy paralysis" on flashcards.
- **Vanity metrics.** Card counts and streaks don't tell you whether you can understand anything.

**What's wrong with Duolingo et al. [G, as an assumption]:** too childish, doesn't get you to native content, and gamified XP is meaningless. Comprehensible-input tools (LingQ, Migaku) are seen as cluttered, overloaded with cards, or too expensive.

## 3. How NeuLang solves it — core mechanics

| Mechanic | What it does | Origin |
|---|---|---|
| Frequency-ranked core lexicon | Backlog of the top ~3,000 content words (nouns, main verbs, adjectives). Function words are excluded as standalone items. | D + G |
| **Multi-word chunks** as items | Collocations, discourse markers and idioms (*por eso*, *en mi opinión*, *darse cuenta*) are first-class items, sourced from n-gram and subtitle corpora. A chunk counts as 1 of the 5 daily words. | **D** (late addition) |
| 5-word daily triage | Swipe through ranked candidates ("already know" / "learn") until 5 unknown words are locked in. | D |
| Image anchoring | For each word: translation in the user's native language, a "where this is from" context line, and 4 images. The user picks the one that fits best, and it becomes that card's permanent image. | **D** |
| Personalized story | ~400–500 words using today's 5 words plus some due review words plus 2 subtle grammar patterns, on topics the user likes. | D (topics, length) + G (constraints) |
| **Chip reader** | Every word (or chunk) is tappable. Tapping shows a translation, turns the word yellow (unknown) and adds it to the Vault. **Bold** marks words currently being learned; tapping one opens its image flashcard. Each word shows an encounter counter. | **D** (core UX he emphasized) |
| Interest feedback | Two-step prompt: "Was this interesting?" then "What do you want to read next?" (free text plus preset chips). This steers future stories. | **D** |
| Textercise | SMS-style chat with the AI. 3 target-word chips sit at the top and get checked off when the user uses them. Bot messages are chips too. | **D** |
| Rapid flush | Ends the lesson. Quick multiple-choice and two-column matching for due words not covered by the story or chat. | **D** |
| Resume | Leave mid-lesson and a "Continue where you left off" popup returns you to the exact step. | **D** |
| AI Companion | Floating 4th button for unlimited free chat, explanations and roleplay. All its words are chips, and tapping one sends it to the Vault and refines the app's model of which words you know. | **D** |
| Passive vocab model | Words seen without being tapped count as familiar; taps count as unknown. This feeds the comprehension % and future selection. | G (extends D's idea) |
| Silent grammar audit | Errors in chat are logged without correcting the user, then modeled in later stories. | G |
| LLM story pipeline | 5 mandatory words, plus the model picks 2–3 from a pool of ~15 due review words, returning JSON. A scenario-first prompt avoids "Mad Libs" stories. | G (answering D's question) |
| Chunk-aware tokenizer | Greedy longest-match against the lexicon so *por eso* renders as a single chip. | G (from D's chunk idea) |

## 4. Main user experience, end to end

**Onboarding — not specified anywhere.** Implied needs: target and native language, interest topics and domains, level and goal (needed for the cohort leaderboard), and some placement step so that "842 / 3,000 known" means something.

**Navigation (final):** a bottom bar with 3 tabs (**Today**, **Vault**, **Profile**) plus a separate chat bubble on the right for the **AI Companion**.

**Today (home):** "You understand 78% of everyday spoken Spanish", known words out of the top 3,000, a 7-day chart of word encounters, and "+N words in the last 7 days". Below that, the **Start today's lesson** card shows what the lesson contains (about 8 minutes) and its state: not started, in progress (resume), or done ("next unlocks at midnight"). At the bottom is a **"Have some spare time?"** carousel of level-matched articles, stories and book excerpts that use due and new words.

**Daily lesson (full-screen, sequential, resumable):**
1. Intro animation, "Let's decide your 5 words of the day!", then the triage deck until 5 are locked.
2. Image anchoring for each of the 5: translation, source line, and a pick from 4 images.
3. Personalized story in the chip reader (with an optional audio button [G]). Then the interest feedback drawer.
4. Textercise: SMS chat with 3 target words to use.
5. Rapid flush: multiple choice and matching for leftover due words.
6. Completion screen showing the streak, the comprehension % change, and a return-home button.

**Vault:** Currently learning (with image thumbnail and review status), Up next (pin to prioritize, swipe to dismiss), an archive of read stories to reread, and the 2 grammar concepts currently being woven into stories, each with a plain-language explanation and examples from the user's own stories.

**Profile:** streak, words in the last 7 days, a ranking against similar-level users with similar goals, and domain roadmaps and badges (e.g. "Business 54/100"). David called these "supplementary".

**Retention levers:** the daily streak, the visible rise in comprehension %, story topics tuned to the user's interests, the resume popup, the consistency-based cohort ranking, badges, the spare-time carousel, and the always-available companion. No notification or reminder strategy is described.

## 5. How the idea evolved, and which version looks final

| # | Topic | Earlier | Later | Looks final |
|---|---|---|---|---|
| 1 | Navigation | 4 tabs: Daily Focus, Vault, Practice Chat, Progress | 3 tabs: Today, Vault, Profile | **3 tabs + separate AI chat bubble** |
| 2 | Daily intake | "5–10 words/day" | "Exactly 5" | **5**, also backed by the story-density analysis |
| 3 | Day structure | Spread across the day: morning reveal, midday story, evening flush, overnight re-ranking | One ~8-min sequential lesson | **Single lesson.** "Morning triage" and "evening Textercise" wording still leaks into the pitch and the last answer. |
| 4 | Lesson contents | **[D] "10 quick flash cards, 1 500-word story, 5 Textercises"** | [G] spec: triage to 5, 1 story, **1** Textercise with 3 words, 1 flush. David's own detailed description also has one chat with 3 header words. | Probably 1 Textercise with 3 words. **"10 flashcards" and "5 Textercises" are unresolved.** |
| 5 | Words per story | Spec: 5 new + **10–12** review words + 2 grammar patterns | Later analysis: 15 targets makes stiff prose. Cap at **5 mandatory + 2–3 review** picked from a pool of ~15 due words. | **5 + 2–3.** The screen spec was never updated. |
| 6 | Function words | Strictly excluded; "No prepositions" in the pitch | Chunks containing them (*por eso*, *a pesar de*, *sin embargo*) become learnable items | **Excluded as single words, included inside chunks.** The pitch line "no prepositions" is now misleading. |
| 7 | Tapping an unknown word | v1: logs the word as "Hard" in SRS | Turns yellow and adds the word to the Vault as a future candidate | **Yellow means add to Vault.** What happens if the word is already in SRS is undefined. |
| 8 | What is bold | [D] Words in the short SRS intervals that were prompted into the story; bold stops once a word matures | [G] Today's words plus all active-SRS words | Close, but [D]'s "stops being bold after a certain point" rule needs defining. |
| 9 | Content sources | YouTube micro-clips, literature excerpts, podcasts, URL importer with chip-grid triage, "word dossier" with provenance stats | Generated stories, plus a carousel of articles, news and book excerpts. Only an audio button remains. | **Mostly generated text.** Importer, YouTube clips and dossier were dropped (not explicitly, just absent). |
| 10 | "Where this is from" | [D] a small note saying where the word was used (a real source) | [G] "Where this word lives: essential for workplace negotiations" (a usage description) | **Unclear.** Real source quote or usage blurb? |
| 11 | Chat | v1 Practice Chat tab (scenario chat + quick translator) | Textercise inside the lesson **plus** the free AI Companion | **Both.** |
| 12 | Grammar | Silent audit, plus a log comparing "your mistakes" with "how we modeled it" | 2 grammar concepts woven into stories, shown in the Vault. Companion has an "Explain grammar" mode. | **2 concepts in stories + explanation on request.** The mistake-log view and how the audit picks concepts are dropped or undefined. |
| 13 | Progress | Progress tab: comprehension %, force-directed domain graph, peer benchmark | Comprehension % and 7-day chart on Home; ranking and badges on Profile | **Home + Profile.** The graph was dropped. |
| 14 | Gamification | "Replaces vanity metrics, no gamified XP or arbitrary leaderboards" | Streaks, leaderboard, badges, "Top 28% of learners" | **Contradiction.** It is softened by ranking on consistency (days completed out of 7) rather than points. |
| 15 | Scope target | "Top 1,000–3,000" | Landing copy: "core 2,000"; home metric: "/3,000" | Top 3,000 seems to be the working number. |
| 16 | Master screen prompt (for Stitch) | Gemini's full screen-flow spec | David: "markdown is incomplete, make it again". **The reply is empty**, so the spec ends at the chat screen. | No onboarding, settings, paywall or empty states are specified. |

## 6. Open questions and gaps to decide later

**Product definition**
1. **Onboarding and placement.** Which languages, native language, interests, level and goal get asked? How is the initial known-word estimate built: a placement test, a fast "already know" sweep, or self-rated level?
2. **Launch languages.** Only Spanish→English is exemplified. Which target languages and native languages ship at v1? The image anchors, lexicon, chunk lists and tokenizer are all per-language work.
3. **Lesson composition.** "10 flashcards / 5 Textercises" (David) or "triage to 5 / 1 Textercise with 3 words" (spec)? Does the Textercise drill today's new words or older ones?
4. **"Don't know" vs "Learn".** David listed three triage buttons (don't know, learn, already know); the spec merged them into two.
5. **One lesson a day only?** Is there an extra lesson for eager users? The test assumptions flag "too slow" churn as a risk.

**Learning engine**
6. **SRS model.** Which algorithm (FSRS, SM-2, custom)? What counts as a review: story exposure, chat use, flush answers, or passively seeing the word? When is a word "mastered"? Does an unverified "Already know" count as mastered and inflate the 842/3,000 metric?
7. **Recall type.** The flush is recognition only (target language → native, multiple choice). Is there any production or recall practice beyond the Textercise?
8. **Story budget.** Confirm 5 new + 2–3 review per story, and how many grammar patterns can fit on top.
9. **Grammar concepts.** How are the 2 per week chosen (from the chat audit, a level syllabus, or something else), and how long does each stay active?
10. **Chunks.** How are chunks surfaced in triage, and what ratio of chunks to single words?
11. **Detecting word use in the Textercise.** It has to handle inflections (*acordar* vs *acordamos*) and typos.

**Content and assets**
12. **Images.** Where do 4 images per word come from (stock, AI-generated, curated), especially for abstract words like *imprescindible* or *plantear*?
13. **Real vs generated content.** David wants "not random nonsensical AI stories", yet the stories are LLM-generated. Are the carousel's news and book excerpts real (which brings licensing) or generated?
14. **Audio/TTS** for stories, and voice input in chat: in or out of v1?

**Business**
15. **Monetization vs AI cost.** The test assumption is a $5–12/month subscription (the smoke test uses $6). There's a daily story, an LLM chat per lesson, and an *unlimited* companion. What is the free tier, and what do the per-user LLM costs look like?
16. **Validation first?** The doc proposes Reddit and app-review mining, a landing-page fake-door test (>15–20% email signups = go), and learner DMs. Does David want to do this before design or build?
17. **Leaderboard.** Keep the consistency ranking given the anti-gamification stance? How are cohorts formed (level and goal), and is there enough user volume for that at launch?

**Design gaps for the Stitch prompt:** onboarding, settings, paywall, notifications, empty states for a new user (no stories, no stats), loading during story generation, error and offline states.
