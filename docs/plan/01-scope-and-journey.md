# 01 · Product scope, user journey and screens

[← Back to plan index](../neulang-plan.md)

Tag legend: **David decided** = binding (from [project-context.md](../project-context.md) or David's own original write-up). **Recommendation — needs David's check** = my proposal, backed by evidence, not yet approved.

---

## 1.1 What NeuLang is, in one paragraph

NeuLang teaches English vocabulary (words *and* multi-word chunks) to non-native speakers through repeated encounters in reading, listening and usage, instead of making users manage decks. Every time a learner meets a word — in a story, a podcast, a chat exercise, a speed-round card — the app logs it. Those logged encounters drive spacing, move words into a long-term "deep" state, and resurface deep words after roughly two months. The daily lesson is made of 5-item **blocks**: 1 block a day on the on-ramp, 2 at the 10/day default. Each block has a story (the main vocabulary engine), a keyed cloze check, an optional "use it" exercise, and a time-capped speed round. Listening is a separate, optional training track.

**External framing (David's planning brief):** "automated sentence mining: the high-frequency words *you* don't know yet, met in real context, tracked word by word". Never "flashcards" or "a vocabulary app". Outside lessons, the user reads level-matched articles and AI stories that contain their words, and can chat with an AI companion.

## 1.2 Decisions in this section

### D1.1 Launch target language — **David decided**
English as a second language (ESL) first; Spanish and French as target languages later. Implications are worked out in [02](02-english-lexicon-and-languages.md).

### D1.2 Navigation: 3 tabs + companion bubble — **David decided** (original write-up)
Tabs: **Today**, **Vault**, **Profile**, plus a floating AI-companion bubble reachable from anywhere except inside an exercise that grades free text (to avoid the companion "doing the homework"). I add one detail: the free-reading library lives inside **Today** as a second section ("Read") rather than a 4th tab, which keeps David's 3-tab structure.

### D1.3 MVP vs later — **Recommendation — needs David's check**

**Options.**
- **A. Everything David described at launch** — including YouTube clips, licensed podcasts, picture description, AI companion, Spanish/French.
- **B. Core loop at launch, risky/expensive inputs later** (recommended).
- **C. Text-only MVP** (no audio, no images).

**Evidence.**
- YouTube's API terms forbid charging users to watch content in an embedded player, and forbid modifying/overlaying the player or downloading audio; non-owned captions can't be downloaded through the API (see [06 §6.5](06-content-pipeline.md#65-youtube-and-podcasts)). A paid YouTube lesson step is therefore a policy problem, not just a build task.
- Commercial podcasts have no general reuse license; audio has to be licensed or self-produced (see [06](06-content-pipeline.md)).
- Education apps retain poorly: about 14–15% day-1 and ~2–3% day-30 retention in Phiture's benchmark [S1]. The core loop has to be excellent before breadth matters.
- Listening is still part of David's lesson design, so dropping it entirely (option C) contradicts a binding decision.

**Recommendation: B.**

| Area | MVP (launch) | v1.1 | Later |
|---|---|---|---|
| Onboarding + adaptive placement | ✅ | Listening sub-check | Re-placement via passive signals only |
| Daily lesson (triage, anchor, main input, use-it, speed round) | ✅ | | |
| Main input: AI stories, VOA/public-domain excerpts | ✅ | | |
| Listening training (separate, optional; not a vocabulary channel) | ✅ AI-produced 2-voice "mini-podcasts" (TTS) + VOA archive audio | | Licensed podcast clips |
| Keyed cloze check (human-written Tatoeba sentences first) | ✅ | | |
| Main input: YouTube clip | ❌ | Free (non-paywalled) "Watch" extras via embedded player, creator partnerships | |
| Use-it: Textercise chat, opinion prompt | ✅ | | Voice answers (STT) |
| Use-it: picture description | ✅ (AI scene images, see [07](07-images.md)) | | |
| Chip reader + free reading library + "articles with your words" | ✅ | URL/paste importer | |
| AI story generator (user picks topic) | ✅ | | |
| AI companion (text) | ✅ with fair-use limit | Roleplay scenarios | Voice mode |
| Vault (word list, encounter timeline, states) | ✅ | Export to CSV/Anki | |
| Profile, streak, word counts ("3,650 this year"); "% of everyday words" only at 6–12-month horizons | ✅ | Badges | Leaderboards / cohorts |
| Paywall, trial, restore purchases, account deletion | ✅ | Web checkout (US) | |
| Notifications (daily reminder, deep-word resurfacing) | ✅ | Smart timing | |
| Offline: downloaded next lesson + review queue | ✅ | Offline reading packs | |
| Native languages | Spanish + Brazilian Portuguese + English-only mode (see [02](02-english-lexicon-and-languages.md)) | +2–3 by waitlist | More |
| Target languages | English | | Spanish, French |
| Grammar tracking | ❌ | | Light "grammar in context" notes |

### D1.4 Lessons per day — **Recommendation — needs David's check**
*Updated for David's planning brief.* New items come in **5-item lesson blocks**, following the intake ladder in [04 D4.8](04-review-model.md#d48-intake-ladder--recommendation--needs-davids-check-adopted-from-davids-planning-brief-replaces-the-earlier-5-new-wordsday):
- **on-ramp 5/day** (1 block) for the first 2 weeks;
- **default 10/day** (2 blocks; the headline);
- **low-energy 5/day** opt-down;
- **15/day** ceiling, unlocked on demonstrated retention.

After the day's blocks, the user can do unlimited **extras**: free reading, AI stories, listening training, the companion. Extra blocks beyond the user's intake setting aren't offered, so the review load stays bounded (*"if they keep learning tons of new cards… they will have to do so many reviews… making them quit"*, the r/Anki failure mode the brief cites). **Never market an intake below 10/day.**
*Why:* The main reason SRS users quit is review pile-up. Capping new items and letting reading be unlimited keeps the "encounters" engine fed without inflating the due queue.

### D1.5 Account timing — **Recommendation — needs David's check**
Use anonymous auth at first launch, so the user can take the placement test and the first lesson with no sign-up. Ask for an account (Apple / Google / email) after the first lesson, framed as "save your progress". Apple requires **Sign in with Apple** whenever another third-party social login is offered (App Review Guideline 4.8) [S2]. It also requires **in-app account deletion** for any app with account creation (Guideline 5.1.1(v)) [S2].

### D1.6 Free vs paid boundary — see [08 §8.4](08-unit-economics.md#84-trial-vs-freemium) (Recommendation — needs David's check)

---

## 1.3 End-to-end user journey

**First session (target: placement + first lesson done in about 15 minutes)**

1. **Install → splash → welcome.** One-sentence promise: "Learn the English words you actually meet — through stories, chats and podcasts." Primary button: *Start*.
2. **Native language.** Choose from the supported list. "My language isn't here" leads to English-only mode (B1+) plus an optional waitlist email.
3. **Goal and pace.** Goal: everyday / work / study / travel / exams. The goal biases the word list (e.g. study adds NAWL academic words; see [02](02-english-lexicon-and-languages.md)).
   - Pace is shown as a count: "**10 words a day = 3,650 this year**" (≈ 30–35 min a day in two sessions).
   - Everyone starts on the 5/day on-ramp, which isn't marketed, and auto-escalates to 10 when the retention check passes.
   - A "busy weeks" option (5/day) is in Settings, not in onboarding.
4. **Interests.** Pick ≥3 of about 40 topic bubbles in 8 groups (double-tap = favourite), optional formats (news, stories, how-to…), and an optional text box for anything specific ("Formula 1"), echoed back as editable chips. These seed content matching **and** the interest-weighted word ranking ([13 §13.3](13-interests-and-feedback.md#133-onboarding-the-first-interest-signal-o5)).
5. **Self-rated level** (one tap: "beginner … advanced"). Used only as the placement test's starting point.
6. **Placement test** (about 5 minutes, see [03](03-placement-test.md)). Ends on a results screen: "You know about 2,300 of the most common English words (± 300). That's ~85% of the words in everyday English. Next: the high-frequency words *you* don't know yet."
7. **Notification primer** (custom pre-permission screen, then the OS prompt). Ask only after the results screen, when motivation is highest.
8. **First lesson** (shortened, about 6 minutes): triage of 5 words → anchor → short story → one chat exercise → speed round.
9. **Lesson complete**, then **save progress** (account) and then the **paywall** with a trial offer (see [08](08-unit-economics.md)). Declining the trial leads to the Lite tier.

**Daily loop (day 2+)**

1. Push notification at the user's chosen time: "Today's story is ready: 5 new words, about 15 minutes." A second reminder for block 2 at a second chosen time.
2. **Today**: shows the lesson card, the review count, the streak, and "Read" recommendations.
3. **Lesson blocks**: about 15 minutes each, 1–2 a day by intake (see [05](05-lessons-and-exercises.md)). They can be resumed from the exact step, as in David's original idea.
4. **After every text** (lesson story, excerpt, article, AI story, podcast): the "Was this interesting?" card (David decided, [13 §13.4](13-interests-and-feedback.md#134-the-end-of-text-review-card-every-text)). After the lesson: recommended articles "with 4 of your words", showing which words.
5. **Free reading / companion**: each tap on a word logs a lookup encounter, and each word read logs a passive encounter.
6. **About 60 days later**, a deep word resurfaces as a single card or a micro-exercise inside the speed round.

**Lifecycle moments to design for** (things David hasn't specified yet):
- Missed days: on return, show a "welcome back" queue capped at 15 reviews and pause new words until the backlog is under 40. Never show "you have 212 reviews".
- Streak protection: one free "streak freeze" per week, so a missed day doesn't kill motivation.
- Trial ending: a reminder 2 days before the charge. Apple and Google don't require it, but it reduces refunds and complaints.
- Subscription lapse: a billing grace period screen. The Vault stays readable and reviews keep working (Lite), but AI features stop.
- Level up: celebrate **word-count milestones** (100, 500, 1,000 learned) and band completions ("Core 3 complete"). Coverage % milestones (90 / 95 / 98%) only after 6+ months of use, because one-month coverage deltas are tiny and easy to disprove (brief).

---

## 1.4 Screens inventory

IDs are used in analytics event names (see [09](09-architecture.md)). The **States** column lists every non-happy state the screen must handle.

### Onboarding and account (O)
| ID | Screen | Purpose | States |
|---|---|---|---|
| O1 | Splash | Load config, check forced update | Offline → cached config; forced-update (E4) |
| O2 | Welcome | Value prop, *Start* / *I have an account* | — |
| O3 | Native language | Pick L1; "not listed" → English-only + waitlist | Search with no results |
| O4 | Goal & pace | Biases word list; explains "10 a day = 3,650 a year" and the on-ramp | — |
| O5 | Interests | ≥3 topic bubbles (★ on double-tap), format row, optional text box echoed back as chips ([13 §13.3](13-interests-and-feedback.md#133-onboarding-the-first-interest-signal-o5)) | Fewer than 3 picked → button disabled with hint; text box offline → parsed on next sync; unsafe request → polite refusal, not stored |
| O6 | Self-rated level | Placement start point | — |
| O7 | Placement intro | "The words get harder as you go. Pick *I don't know* instead of guessing — it makes your lessons fit better." | — |
| O8 | Placement item (sentence + 4 native-language options + "I don't know") | Adaptive ladder of harder and harder real words; confirmation items in reverse format; phrase and sense probes ([03 §3.3](03-placement-test.md#33-test-design-about-5-minutes-3545-items)) | Offline → bundled item bank works offline; app killed → resume; random tapping detected → gentle note on results |
| O9 | *(merged into O8)* | — | — |
| O10 | Placement results | Word count ± range, coverage %, CEFR band, "words you'll learn first" | Low-confidence result → "We'll fine-tune as you learn" |
| O11 | Notification primer | Pre-permission screen | Denied → stays usable; settings deep-link later (E7) |
| O12 | Save progress / account | Apple, Google, email; skip allowed once | Auth failure; email already used; merge anonymous data |
| O13 | Paywall | Trial offer, annual vs monthly, restore | Products fail to load → retry + "continue with Lite"; purchase pending/deferred (Ask to Buy); already subscribed |
| O14 | Trial started | Confirms trial end date and reminder | — |

### Today (T)
| ID | Screen | Purpose | States |
|---|---|---|---|
| T1 | Today home | Lesson card, review count, streak, coverage %, "Read" shelf | Lesson generating (skeleton); lesson ready; lesson done ("extras" mode); backlog mode ("reviews first"); offline with downloaded lesson; offline without one (E1); new user with empty Read shelf |
| T2 | Lesson preview | Today's 5 words, steps and estimated minutes; swap a word | Word swapped → regenerate step (spinner + fallback) |
| T3 | Resume prompt | "Continue where you left off?" | Lesson content changed after an algorithm update → restart current step |
| T4 | Read shelf / library | Recommended articles, "with your words" (word chips on each card), AI stories, topics, "Because you asked for…" labels, **Request a text** (same bubbles + text box as L9) | Empty for topic → suggest neighbours; loading; error |

### Lesson (L)
| ID | Screen | Purpose | States |
|---|---|---|---|
| L0 | Lesson loading | Pre-fetch assets | Slow network → start with text steps, stream audio later |
| L1 | Triage deck | "Don't know / Learn / Already know" for candidate words (David's original idea) | "Already know" → quick check card (1 MCQ) so it isn't a free pass; all known → pull more candidates |
| L2 | Anchor | Pick 1 of 4 images, or see a gloss plus an example for abstract words | Image failed to load → text-only anchor |
| L3 | Story / excerpt reader | Chip reader with target words in bold | Audio unavailable → hide play button; very long word tap list |
| L3c | Cloze check | 5–8 answer-keyed fill-in-the-blank items (main promotion source) | Sentence report → swap sentence |
| L4 | Listening training (optional, from Today) | Mini-podcast player, transcript toggle, 2 comprehension Qs | Audio fails → transcript-only mode, marked as "read" encounter |
| L4b | Production recap card | Learner self-rates each produced word (Nailed it / Not sure / Didn't know) — the only route by which production promotes | Flagged word → only the last two options |
| L5 | Textercise chat | Chat using 3 target-word chips | LLM timeout → canned fallback prompts with local grading; abusive input → moderation message |
| L6 | Opinion prompt | Short written answer using target words | Same as L5; skip allowed |
| L7 | Picture description | Describe image using the word | Image missing → swap to opinion prompt |
| L8 | Speed round | Time-capped (~4 min per block) fast cards | Nothing due → only this block's 5 items; budget hit → "The rest will wait — no pile-up" |
| L9 | "Was this interesting?" card | After **every** text: 👍 / 😐 / 👎; 👎 opens topic bubbles, reason chips and a big text box ([13 §13.4](13-interests-and-feedback.md#134-the-end-of-text-review-card-every-text)) | Never blocks completion; compact one-row mode after 5 skips; offline → queued; "Got it — next text will be about X" confirmation |
| L10 | Lesson complete | Words learned, encounters today, coverage change, streak | Offline → queued sync banner |
| L11 | Exit confirm | "Your progress is saved — resume any time" | — |
| L12 | Grading appeal | "I think my answer was right" | Sent → thanks; offline → queued |

### Reading (R)
| ID | Screen | Purpose | States |
|---|---|---|---|
| R1 | Reader | Chip reader: tap = translation, yellow highlight, Vault add, bold = learning; encounter counter; **phrases underlined as one unit** (incl. separable "pick *the kids* up"), tap shows the phrase meaning first; sense-aware glosses ([14 A7–A8](14-gaps-and-test-plans.md#141-gaps-where-users-may-feel-lost-or-the-system-doesnt-connect)); end-of-text "you met 4 of your words" summary, then L9 | Text too hard (coverage <90%) → "Make this easier" (simplified version if licence allows, see [06](06-content-pipeline.md)) |
| R2 | Word popup | Gloss in L1, example, audio, "add to Vault", encounter count | No gloss for rare word → English definition + "report" |
| R3 | AI story generator | Pick topic/genre/length; uses your words | Generation failure → retry + pre-made story; unsafe prompt → refusal copy |
| R4 | Source & attribution | Licence, author, link back, "AI-generated" label | — |
| R5 | Report content | Wrong translation / offensive / factual error | — |

### Companion (C)
| ID | Screen | Purpose | States |
|---|---|---|---|
| C1 | Companion chat | Free conversation using your words: the companion gets up to 5 fading words to weave in; "Chat about this story" entry from every story end; optional recap card when the chat ends, so confirmed uses count ([14 B1](14-gaps-and-test-plans.md#b-parts-of-the-system-that-dont-talk-to-each-other)); corrections on request | First open → AI disclosure (EU AI Act Art. 50); slow responses; moderation |
| C2 | Roleplay picker (v1.1) | Scenarios: job interview, ordering food… | — |
| C3 | Fair-use sheet | "You've chatted a lot today — back tomorrow" | Shown only past cap (see [08](08-unit-economics.md)) |

### Vault (V)
| ID | Screen | Purpose | States |
|---|---|---|---|
| V1 | Vault home | Words and phrases by state, with **plain labels**: New / Learning / Getting stronger / Locked in / Mastered / Already knew ([14 A2](14-gaps-and-test-plans.md#141-gaps-where-users-may-feel-lost-or-the-system-doesnt-connect)); search, filters (topic, phrase type, function); "Practise these" and "Find me a text with these words" | Empty (new user) → explain how words get here (V4) |
| V2 | Word detail | Gloss, "why this word?", examples from texts you read, encounter timeline (each dated encounter with a link to its source), "next check: about Thursday — sooner if it turns up in your reading", steps left before *Locked in*, listening ("ear") badge | Long timelines → grouped by day |
| V3 | Lesson history | Past lessons, re-open story | Empty |
| V4 | Vault empty state | Illustration + "Tap any word while reading to save it" | — |

### Profile (P)
| ID | Screen | Purpose | States |
|---|---|---|---|
| P1 | Profile | Three headline numbers: **Words you know** (placement estimate + learned, with range; "Learned with NeuLang: N · on pace for X this year"), **Memory strength** (% of keyed checks passed, 30 days), **Streak**; CEFR band; weekly chart ([00 §0.9](00-how-it-all-works.md#09-step-8--what-the-user-sees-about-their-progress)) | — |
| P2 | Progress detail | Word journey bar by state, band progress (phrases as their own bar), words per interest topic, reading/listening stats; coverage % only after 6 months | Too little data → "Come back after 3 lessons" |
| P5 | Your interests | Topic chips with strength bars (boost, pin, remove), format slider, custom interests with status, past requests and what happened to them ([13 §13.8](13-interests-and-feedback.md#138-closing-the-loop-showing-users-their-interests-are-used)) | No requests yet → "Ask for anything from the end of any text" |
| P3 | Settings | Account (email, sign-in, delete account); Subscription (status, manage, **restore purchases**); Learning (pace: 10/day default · 5/day "busy weeks" · 15/day when unlocked; **Writing practice on/off**; **AI chat exercises on/off**; **show/hide AI companion**; **Prefer real texts**; goal, interests, native language, gloss language, retake placement); Subscription also has **one-tap Cancel** (deep link to the store's subscription page); Audio (voice, speed, autoplay); Notifications (time, types); Privacy (data export, delete chat history, analytics opt-out, personalised-content toggle); Help (FAQ, contact, report bug); About (licences, content attributions, AI-use notice, terms, privacy policy) | Changing native language → warns glosses change, keeps progress |
| P4 | Retake placement / reset | Re-run test or reset all progress | Confirm destructive action |

### System (E)
| ID | Screen / component | Trigger |
|---|---|---|
| E1 | Offline banner + offline Today | No network; shows downloaded lesson or reviews-only |
| E2 | Generation failed | LLM error → pre-built fallback lesson from corpus |
| E3 | Generic error | Retry + report |
| E4 | Maintenance / forced update | Remote config flag |
| E5 | Billing issue / grace period | Store reports billing retry |
| E6 | Content report sheet | From any text/exercise |
| E7 | Permission denied helper | Notifications/mic denied → deep-link to OS settings |
| E8 | Empty-list pattern | Shared component for Vault, history, library |

---

## Sources
- [S1] Phiture / Business of Apps, education-app retention benchmarks — https://www.businessofapps.com/data/app-retention-rates/ (education: D1 ≈ 14–15%, D30 ≈ 2–3%)
- [S2] Apple App Review Guidelines (4.8 Login Services; 5.1.1(v) account deletion) — https://developer.apple.com/app-store/review/guidelines/
- YouTube API Services Developer Policies — https://developers.google.com/youtube/terms/developer-policies
