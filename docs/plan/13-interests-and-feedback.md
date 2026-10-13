# 13 · Interests and feedback: how we learn what each user wants to read

[← Back to plan index](../neulang-plan.md) · Plain-language version: [00 How it all works](00-how-it-all-works.md) · Ranking maths: [12 §12.5](12-vocabulary-database.md#125-how-interests-change-what-counts-as-most-common)

**David decided (2026-10-13):**
- **Every text the user reads ends with a short "like" review**: was this interesting, do you want more like this?
- If they didn't like it, they can say what they want instead, using:
  - **topic bubbles** they can tap if they don't feel like typing (news, self-help, entrepreneurship/business, environment, technology…);
  - **a big text box** to write what they want to read about.
- Interests change **which words count as most common** for the user, not just which texts they get.

Everything else here is **Recommendation — needs David's check**. This replaces the earlier "interest feedback at most once a day" (L9).

---

## 13.1 What we need to know, and why it's two things, not one

"I want more news" and "I want more about the environment" sound alike, but they're different:
- **Topic** = what the text is *about*: environment, business, football.
- **Format** = what *kind* of text it is: news, story, how-to, opinion, interview/dialogue.

A user can want environment *news* but not environment *stories*. So every text carries both labels, and every user has a weight for both. The bubbles mix both kinds on purpose, because that's how people think ("news", "self-help"). The system files each bubble under the right dimension.

Two other things travel with feedback but are **not interests**, and must never be stored as interests:
- **Difficulty** ("too hard", "too easy") goes to the level model.
- **Length** ("too long") goes to the length preference.

Mixing these up is the most common way recommendation systems go wrong here. A user who says 👎 because a business article was too hard doesn't dislike business.

## 13.2 The interest taxonomy

About **40 topics in 8 groups**, plus **6 formats**. The same labels are used at three points: tagging corpus texts, building per-topic word frequencies ([12 §12.5](12-vocabulary-database.md#125-how-interests-change-what-counts-as-most-common)) and showing bubbles.

| Group | Topics |
|---|---|
| **Business & money** | Entrepreneurship & startups · Personal finance · Marketing · Careers & the workplace · Economy |
| **Self & mind** | Self-help & productivity · Psychology · Relationships · Health & fitness · Mindfulness |
| **Science & tech** | Technology · AI · Science · Space · Environment & climate |
| **News & society** | World news · Politics · History · Education · Law & true crime |
| **Culture** | Movies & TV · Music · Books · Gaming · Anime, K-pop & fandoms |
| **Lifestyle** | Food & cooking · Travel · Fashion & beauty · Home & DIY · Pets & animals |
| **Sports** | Football (soccer) · Basketball · Combat sports · Motorsport · Other sports |
| **Everyday life** | Daily conversations · Family & parenting · Shopping & services · City life · Humour |

**Formats:** News · Stories (fiction) · Explainers · How-to & advice · Opinion · Conversations & interviews.

Each topic has a two-level structure where useful (Sports → Football), as in [06 §6.7](06-content-pipeline.md#67-matching-content-to-users). Users never see more than one level of bubbles at a time.

**Custom interests** (from the text box, §13.5) sit beside the taxonomy: "Formula 1", "Brazilian startups", "Taylor Swift". Each is mapped to its nearest taxonomy topics (Formula 1 → Motorsport) so it feeds word frequencies, and it keeps its own text embedding so it can find texts the taxonomy can't.

## 13.3 Onboarding: the first interest signal (O5)

Screen O5, "What do you like reading about?":
- **Bubbles:** all 8 groups visible, each showing 3 topics with a "+ more" to expand. Pick at least 3. Tapping twice marks a topic as a **favourite (★)**, which sets a stronger starting weight.
- **Format row:** "I like…" News · Stories · How-to · Conversations, multi-select, optional.
- **Big text box (optional):** "Anything specific? e.g. *Formula 1*, *running a coffee shop*, *Korean dramas*". Parsed as in §13.5, and **shown back as chips before continuing** ("We'll look for: Motorsport · Business · *Formula 1*"). The user can delete a wrong chip.
- **The goal** (Conversation / Work / Study / General, already on O4) also adjusts the starting weights: Work → Careers, Business; Study → Science, Education.

**Starting weights:** ★ favourite 1.0 · picked 0.7 · named in the text box 0.8 · from goal 0.3 · everything else 0.1 (so exploration has something to work with).

## 13.4 The end-of-text review card (every text)

**Shown after:** lesson stories, real-text excerpts, free-reading articles, AI stories, explainers and mini-podcasts. Not shown after cloze items, speed rounds or companion chats, because those aren't "texts the user chose to read".

### Layout

**Step 1 — one tap (always shown):**

> **Was this interesting?**
> 👍 Yes, more like this  ·  😐 It was OK  ·  👎 Not for me
> *(skip)*

**Step 2 — depends on the answer:**

| Answer | What opens | Required? |
|---|---|---|
| 👍 | A slim row: "What did you like?" chips: *the topic* · *this kind of text* · *the characters* (series only) · *the level was right*. Plus "Anything you'd like next?" (opens the text box) | No. Auto-continues after 4 s |
| 😐 | A slim row: "Want something different next time?" → tapping it opens the 👎 sheet below | No |
| 👎 | **Bottom sheet** (below) | No; "Skip" is always there |

**The 👎 sheet — "What would you rather read?":**
1. **Topic bubbles** (one tap each, multi-select). The first row is personalised: the user's top interests they haven't seen lately, plus David's examples as defaults: **News · Self-help · Entrepreneurship & business · Environment · Technology**. "More topics" expands the full taxonomy.
2. **"What was wrong with this one?"** reason chips, multi-select:
   *Boring topic* · *Didn't like the story* · *Too hard* · *Too easy* · *Too long* · *Felt fake / AI-written* · *Something was wrong (report)*.
3. **Big text box:** "Tell us what you'd like to read about…", 3 lines tall, with rotating examples ("…how to negotiate a raise", "…the World Cup", "…climate solutions that work"). Voice input uses the keyboard's built-in dictation.
4. **Done** button. The sheet closes and shows **"Got it — your next text will be about Entrepreneurship"** whenever the next text can actually change (§13.7). Feedback must never disappear into nowhere.

**Rules.**
- **Never blocks progress.** The block's completion screen (L10) appears regardless of whether the user answers.
- **Fatigue control while still asking every time** (David decided "every text"): after 5 skips in a row the card shrinks to a single inline row of three emoji under the text, with no step 2 unless tapped. It goes back to full size after any answer.
- **Lesson stories are personal:** a 👎 on a lesson story also asks (one chip row) whether the problem was the *topic* or the *story itself*. The words were chosen for the learner, so the story is worth re-planning, not just the topic.
- **Shared and corpus texts:** ratings are also stored per text and feed content quality (a text with many 👎 and "felt AI" gets reviewed, see [06 §6.8](06-content-pipeline.md#68-qa-and-safety-chain-for-generated-content)).

### Also available outside the card
- **Read tab → "Request a text"**: the same text box and bubbles, at any time.
- **Profile → Your interests** (§13.8).

## 13.5 Turning the text box into something the system can use

The free text goes through one cheap LLM call (a new `classify` task on the gateway, [09](09-architecture.md)) that returns structured JSON:

```json
{
  "topics": [{"id": "business.entrepreneurship", "confidence": 0.9},
             {"id": "lifestyle.food", "confidence": 0.6}],
  "custom": ["running a coffee shop"],
  "formats": ["howto"],
  "difficulty_hint": null,
  "is_request_for_specific_text": true,
  "safety": "ok"
}
```

- **Topics with confidence ≥ 0.6** update weights. Lower-confidence ones are only suggested back to the user as chips.
- **Custom interests** become a row in `user_custom_interests` with an embedding. They're used:
  - to search the corpus by embedding for matching texts;
  - as the topic seed when the planner writes the next AI story or explainer;
  - mapped to taxonomy topics for word frequencies.
- **Difficulty hints** in free text ("simpler please") go to the level model, never the interest model.
- **Safety:** moderation runs first. Disallowed requests get a polite "We can't write about that" and are not stored as interests.
- **Echo back:** the user always sees what we understood, as editable chips. This is both the accuracy check and the "you were heard" moment.
- **Offline:** the text is saved and parsed on the next sync.

## 13.6 How each signal changes the user's interest weights

Each topic weight *w* lies between 0 and 1. Updates are small and additive, and **explicit signals count more than behaviour**:

| Signal | Effect | Why |
|---|---|---|
| 👍 | +0.10 to the text's topics; +0.05 to its format | Positive, but people also 👍 a text for its plot |
| 👍 + "the topic" chip | +0.20 to its topics | Explicit |
| 😐 | No change | Neutral by design |
| 👎 with no reason | −0.05 to its topics and format | Ambiguous: could be the topic, the story, or the level |
| 👎 + "Boring topic" | −0.20 to its topics | Explicit |
| 👎 + "Didn't like the story" | −0.05 to its genre (e.g. mystery); topics unchanged | It's the story, not the subject |
| Topic bubble tapped in the sheet | +0.30 to that topic, and it's **guaranteed to appear in the next 3 texts** | Explicit request, so it must be visible soon |
| Free-text topic (confidence ≥ 0.6) | +0.30, plus a custom interest | Explicit |
| "Too hard" / "Too easy" | **Level model:** the target coverage band moves by ±1 point (e.g. 96% → 97%) | Not an interest |
| "Too long" | Length preference −15% for that format | Not an interest |
| "Felt fake / AI" | Raises the "Prefer real texts" mix by one step; the text goes to QA | Content trust |
| Opened a free-reading text from a topic | +0.03 | Weak behaviour signal |
| Read to the end (free reading) | +0.03 | Weak |
| Abandoned in the first 20% (free reading) | −0.03 | Weak; could be interruption |

**Decay.** Weights drift back toward the user's onboarding baseline:
- behaviour-driven changes have a half-life of about **30 days**;
- explicit choices (bubbles, text box, ★) have a half-life of about **90 days**;
- the user can pin a topic from the interests page so it never decays.

This lets interests change ("I was into the World Cup for a month") without the system forgetting someone's core tastes.

**Exploration.** About **15% of recommendations** and about **1 in 7 lesson stories** come from topics *next to* the user's top ones (Entrepreneurship → Marketing, Personal finance) or from the most-liked topics among similar learners. Without this, the system narrows forever and never learns anything new about the user. Exploration texts carry a small "Something new" label, and their feedback counts double.

## 13.7 How interests shape texts, stories and words

Interests do three jobs, on three different clocks. The user needs to be told this, or they'll expect new words the moment they tap "Business".

| What changes | How ([links]) | How fast the user sees it |
|---|---|---|
| **Free-reading recommendations** | `interest_match` term in the text score, 0.35 of the total ([06 §6.7](06-content-pipeline.md#67-matching-content-to-users)) | **Immediately**: the Read list re-ranks on the next open |
| **Lesson story topics** | The story planner (below) | **Next block** if the new topic was tapped explicitly (the next 3 texts are guaranteed); otherwise within a day or two |
| **Which words are taught** | Personal frequency f_u in the gain score ([12 §12.4–12.5](12-vocabulary-database.md#124-ranking-words-and-phrases-on-one-scale)) | **Gradually, over about a week.** The ranking is recomputed nightly, but the next ~7 days of words are already in the pool ([04 §4.8](04-review-model.md#48-load-control-and-intake-avoiding-review-pile-ups)), and core protection keeps 4 in 5 new words general |

**Story planner: topic and words are chosen together.**
1. Pick tomorrow's story **topic**: sample from the user's interest weights (with the 15% exploration and any guaranteed topics).
2. From the ±7-day pool, pick the **5 items that fit that topic best** (by per-topic frequency and embedding fit), still respecting the ranking order within ±7 days.
3. Add 2–3 **review words** that are close to being forgotten and also fit the topic ([04 §4.6](04-review-model.md#46-matching-reading-to-words)).
4. If fewer than 3 pool items fit the topic, **the words win**: the planner chooses the topic that fits the pool best among the user's top 3 interests. Learning order is protected over topic choice.

**Why the word list changes with interests (in one sentence):** a word that's ranked 2,600th in general English but 300th in business writing will be met far more often by someone who reads business texts, so learning it earlier pays off sooner for them. That's f_u in [12 §12.5](12-vocabulary-database.md#125-how-interests-change-what-counts-as-most-common).

## 13.8 Closing the loop: showing users their interests are used

Most of the "disconnected" feeling comes from feedback that has no visible effect. Fixes, all cheap:
- **"Because you asked for…" labels** on texts that were picked or written because of a bubble or request: "Because you asked for *Entrepreneurship*".
- **"Why this word?" chip** on new words in triage: "Common in *business* texts you like" vs "One of the 1,000 most common English words".
- **Profile → Your interests page:**
  - topics as chips with a strength bar; tap to boost, pin or remove;
  - formats ("More news / fewer stories" slider);
  - custom interests, each with **status**: "*Formula 1* — 4 texts read · 2 new this week";
  - your requests from the text box and what happened to them ("You asked for *negotiating a raise* → 1 explainer written, 21 Oct").
- **Weekly recap line:** "This week you read 3 business texts and learned 6 business words (*revenue*, *invest*, …)".
- **When we can't serve a request yet:** "We don't have *Formula 1* texts at your level yet. We'll write one tonight." Then actually generate one in the overnight batch.

## 13.9 Content operations: demand vs supply

Requests are product research. A small internal dashboard (same admin console as [12 §12.7](12-vocabulary-database.md#127-maintenance-keeping-the-database-right-over-time)) shows:
- **demand** by topic × level × format (weights, bubble taps, text-box requests);
- **supply**: human-written corpus texts available per cell, and their average rating;
- **gaps**: cells where demand is high and supply is thin. These go to the corpus ingestion queue (licensed or public-domain sources from [06](06-content-pipeline.md)) or to shared AI explainers;
- **most-requested custom interests** that should become taxonomy topics;
- **texts with the most 👎 or "felt AI"**, which go to review.

## 13.10 Data model

```text
interest_topics(id, group, parent_id, name_en, kind[topic|format|genre], active)
user_interests(user_id, topic_id, weight, explicit_weight, behaviour_weight, pinned bool,
               baseline, updated_at)                                   -- replaces the simpler table in 09
user_custom_interests(id, user_id, text, parsed_json, embedding, mapped_topics_json,
                      status[active|removed], created_at)
text_feedback(id, user_id, text_id, text_kind[lesson_story|excerpt|reader|ai_story|explainer|podcast],
              rating[up|meh|down|skipped], liked_chips[], reason_chips[], topics_tapped[],
              free_text, parsed_json, shown_compact bool, created_at)
content_requests(id, user_id, source[feedback|read_tab|interests_page], text, parsed_json,
                 status[open|served|cant_serve], served_text_ids[], created_at)
text_topics(text_id, topic_id, score)                                  -- set at ingest/generation
```

Analytics events: `text_feedback_shown{text_kind, compact}`, `text_feedback_given{rating, n_reasons, has_text}`, `interest_bubble_tapped{topic, surface}`, `content_request_served{latency_days}`, `because_label_seen`, `interests_page_opened`.

**Privacy.** The free text is personal data. It's shown in the data export, deleted with the account, never shared with advertisers, and only sent to LLM providers with their no-training / zero-data-retention options switched on (a gateway setting, [09](09-architecture.md)). The personalised-content toggle in Privacy settings turns off behaviour-based updates. Explicit choices still work.

## 13.11 Risks specific to this part

| Risk | Mitigation |
|---|---|
| **Feedback fatigue** (asking after every text) | One tap; never blocks; compact mode after 5 skips; visible effects so answering feels worth it |
| **Misreading 👎** (topic vs story vs level) | Separate reason chips; small default penalty without a reason; difficulty routed to the level model |
| **Echo chamber** | 15% exploration; core protection on words; adjacent-topic suggestions |
| **Thin corpus for a requested topic** (cold start, [10 R5](10-metrics-risks-roadmap.md)) | AI explainers and stories fill gaps; demand dashboard drives ingestion |
| **Users expecting new words immediately** | The three-clocks table (§13.7) is explained in one line on the interests page: "New texts right away · new words over the next week" |
| **Requests for unsafe or unsuitable content** | Moderation before storing; general-audience default |

How we'll check that this works (interest-model accuracy, the topic-weighting A/B test, feedback fatigue) is in [14](14-gaps-and-test-plans.md#148-test-plan-interests-and-feedback).
