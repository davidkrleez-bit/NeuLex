# NeuLang project context

Stable goals, constraints and decisions. Background on the original idea: [neulang-understanding.md](/cursor/stores/bc-696488b9-b4f6-476b-8516-fc6441b38b18/internal/neulang-understanding.md) (source: [neulang-source.md](/cursor/stores/bc-696488b9-b4f6-476b-8516-fc6441b38b18/internal/neulang-source.md)).

## Goal

A vocabulary-first language app that teaches words through repeated, multimodal encounters (reading, listening, usage) instead of deck management. Built in Flutter. Currently in planning.

## Decisions from David (2026-10-13)

- **Placement test:** a quick adaptive test. Starts with "Do you know this word? yes / no", plus a sentence that uses the word followed by a multiple-choice question about its meaning in the user's native language, and similar formats. It begins with easy words, gets harder, then digs into the word clusters, topics and difficulty bands where the user shows gaps.
- **Launch language:** English as a second language first. Spanish and French come later.
- **Lesson is a multimodal mix**, not too many parts at once so it doesn't overwhelm:
  - a story, sometimes a podcast, sometimes a short YouTube section containing target words;
  - text exercises;
  - an opinion prompt shaped so the answer naturally uses the target words (e.g. "Do you believe we are doing enough to help our environment?");
  - a picture interpreting a word that the user describes using that word (e.g. a student copying another's test for "cheating");
  - a short newspaper, article or story excerpt to read;
  - a final speed round of flashcards for words that still need review.
- **Beyond lessons:** recommended articles at the user's level, articles that use their words, and AI-generated stories.
- **Review model:** spacing close to Anki's, but driven by how many times the user *encounters* a word. Once a word has had enough encounters it moves to a "deep" state. If a deep word isn't encountered for about 2 months, it comes back as a flashcard or short exercise. The app must track every word encounter per user with timestamps. Repeat encounters of the same word on the same day count for less.
- **Images per word:** undecided. Possibly scraped stock images. Picking a picture for a word is seen as valuable, but its priority is unclear.
- **Content:** a mix of:
  - real stories and news simplified by AI for lower levels;
  - real texts used as-is;
  - stories written purely by AI.

  The plan is to collect a large corpus of texts, articles and stories, and match them to each user's interests and level. Above-level content is made accessible so reading stays relevant, not just fable-style stories.
- **Pricing:** $8/month.
- **Validation:** skipped. We're building it.
