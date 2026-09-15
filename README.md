# PRD: Blind Briefing — a mini game about AI and unified recruiting data

## Summary

Blind Briefing is a single-file HTML mini game that makes one argument through play: AI in recruiting is only as good as the data underneath it. The player reads pairs of anonymous AI answers to real recruiting questions and votes for the better one. At the end, the game reveals that one assistant sat on a fragmented stack of point tools while the other sat on one unified system, and shows the player which one they trusted. The game runs entirely in the browser, saves progress on the device with no login, is designed for phones first, and holds up in a desktop Chrome window at any size. Target playtime is 3 to 5 minutes.

Every round is built on a real capability Ashby sells against Greenhouse and Lever: native analytics, AI talent rediscovery, candidate surveys, scheduling load balancing, and quality-of-hire data. The fragmented assistant fails in exactly the ways a stitched-together stack fails.

## Goals

1. The player concludes on their own, before being told, that answers grounded in complete data are better. The reveal confirms a judgment they already made.
2. The player can name at least one concrete thing a unified system does that a fragmented stack cannot, because they saw it fail.
3. The game is finishable in under 5 minutes on a phone, in one hand, with taps only.
4. The file can be sent as an attachment or hosted anywhere, opens instantly, and works offline after first load.

Non-goals: no live LLM calls, no accounts, no backend, no leaderboards, no analytics beyond an optional local play counter.

## Player and framing

The player takes the role of Head of Talent at Meridian, a fictional 300-person software company that is hiring hard. The intro screen establishes the setup in three sentences: you have two AI recruiting assistants, both answer every question you ask, and your job is to pick the answer you would act on. It does not say the assistants differ in what data they can see. That is the twist.

The two assistants are never labeled A and B on screen during play. Each round shows two answer cards in randomized order with no persistent identity, so the player judges answers, not personalities, and cannot pattern-match by position or name.

## Game flow

The game has four phases.

Intro (15 seconds). Title, three-sentence setup, one button: Start. If a saved game exists, the button reads Continue and a secondary text link reads Start over.

Rounds (7 rounds, about 30 seconds each). Each round shows the question at the top, phrased as something the player asks both assistants. Below it, two answer cards stacked vertically. The player reads both and taps one to vote. The tapped card gets a selected state and a Lock in button appears; tapping Lock in records the vote and advances with a short transition. A progress indicator (Round 3 of 7) sits at the top. There is no feedback on whether the vote was right, because that would break the blind.

Reveal (45 seconds). The game shows the tally: for example, "You picked the same assistant 6 out of 7 times." It then explains the difference: one assistant could only see one tool at a time across a stack of four disconnected products (an ATS, a scheduling tool, a sourcing tool, and a spreadsheet for reporting); the other saw everything in one system. The closing line states the argument plainly: the AI was the same, the data was not.

Debrief (optional, player-paced). The reveal screen lists all 7 rounds. Tapping a round expands it to show both answers labeled at last (Fragmented stack / Unified system), the player's pick, and one line stating what the fragmented assistant could not see. This is where the Ashby mapping becomes explicit. A final card offers Play again.

Time budget: 15s intro + 7 × 30s rounds + 45s reveal = roughly 4 minutes, with the debrief as optional overtime. Rounds must be readable in under 30 seconds, which sets the copy limits below.

## Round content

Rounds escalate. The first two are questions any AI can answer from a single system or no data at all, and both answers are good; this builds the false confidence the reveal punctures. From round 3 on, every question requires joining data across systems, and the fragmented assistant's answers become confident but wrong or empty. The wrongness must always be a data problem, never a stupidity problem, because the argument is about data, not model quality.

| # | Player's question | What the good answer requires | How the fragmented assistant fails | Real capability it maps to |
|---|---|---|---|---|
| 1 | How many open roles do we have right now? | One number from the ATS | It doesn't; both answers match | Baseline ATS data |
| 2 | Draft a polite rejection email for a candidate we're passing on | No data, just writing | It doesn't; both drafts are fine | Generic AI writing, the "decorative" layer |
| 3 | Where in the funnel are we losing the most engineering candidates? | Pass-through rates by stage across the full pipeline | Gives a generic industry claim ("usually the onsite") with no numbers; the unified answer cites the actual stage with its pass-through rate | Native analytics, no spreadsheet exports |
| 4 | Who from past pipelines should we re-engage for the new platform PM role? | Past candidates joined with interview scores, rejection reasons, and outcomes | Confidently recommends someone the team rejected for cause last quarter; the unified answer surfaces a silver-medalist who scored well but lost on timing, with citations | AI talent rediscovery on historical candidate data |
| 5 | Why did our offer-accept rate dip last quarter? | Offer data joined with candidate survey feedback and scheduling timestamps | Guesses "competitive market conditions"; the unified answer cites candidate survey comments about slow scheduling and the average days between onsite and offer | Candidate experience surveys plus analytics on one data model |
| 6 | Which interviewers are overloaded this month? | Interview load across everyone's calendars | Can only see interviews booked through its own tool and reports "load looks balanced"; the unified answer names two overloaded interviewers with their weekly counts | Scheduling with interviewer load balancing |
| 7 | Are the engineers we hired through referrals actually performing better? | Post-hire quality-of-hire data tied back to source | Has nothing past the offer letter and says so, or extrapolates from resume quality; the unified answer cites 90-day quality-of-hire survey scores by source | Quality-of-hire data captured close to the point of hire |

Copy rules for answers. Each answer is 2 to 4 sentences, under 60 words. The unified assistant's answers include 1 or 2 small citation chips under the text (for example, "Interview feedback · Mar 14" or "Candidate survey · Q2"), because receipts are part of what the player should learn to look for. The fragmented assistant's answers are fluent and confident; they never hedge more than the unified ones, and they never sound dumber, only less informed. All content is pre-written and shipped inline as a JSON array in the file; there are no live model calls, so the game behaves identically in every demo.

Order randomization uses a seed generated at game start and stored with the save, so card order stays stable if the player reloads mid-game.

## In-game interactions

Every interaction is a tap. The full input vocabulary is: tap Start or Continue, tap an answer card to select it, tap Lock in to confirm, tap a round in the debrief to expand it, tap Play again. There is no typing, no dragging, no swiping, no timers, and no hover-dependent behavior.

Selection is a two-step commit (select, then lock in) to prevent accidental votes from scroll-taps on mobile. Tapping the other card moves the selection. Lock in is disabled until a card is selected; if the player taps it with nothing selected, a 13px inline message under the button says "Pick an answer first."

Cards must be fully readable without expanding. If an answer overflows the viewport, the page scrolls; cards never scroll internally.

Transitions between rounds are a 200ms fade and slide, disabled entirely when the OS reports reduced motion.

## Scoring and reveal logic

Each vote records which underlying assistant produced the picked answer. Rounds 1 and 2 are excluded from the headline tally because both answers are equivalent there; the reveal counts rounds 3 through 7. Reveal copy branches on the count of unified picks out of 5:

- 4 or 5: "You picked the same assistant 4 out of 5 times. Here's what it had that the other didn't."
- 2 or 3: "You split your votes. Both assistants sounded confident. Only one could back it up."
- 0 or 1: "You mostly picked the assistant that sounded surest. It was also the one flying blind. Here's what it couldn't see."

All three branches land on the same closing line: "Same AI. Different data. That's the whole difference." Every branch serves the argument, so there is no losing outcome, only a sharper or softer version of the lesson.

## Saving progress

Persistence uses localStorage, not cookies, because nothing is sent to a server. One key, blindBriefing.v1, holds a JSON object:

- schemaVersion: integer, currently 1
- seed: the randomization seed
- currentRound: 0 to 7
- votes: array of {round, pickedAssistant, cardPosition}
- completed: boolean
- lastResult: the tally from the most recent completed game
- playCount: integer

Behavior: the game writes after every lock-in, so a reload mid-game resumes at the current round with prior votes intact. Play again resets round state and votes but keeps playCount and lastResult. Start over from the intro clears round state after a confirm tap. If localStorage is unavailable (private mode, quota, embedded webview restrictions), the game falls back silently to in-memory state; everything works except resume-after-reload, and no error is shown. A schemaVersion mismatch discards the save and starts fresh rather than attempting migration.

## Responsive design

The layout is a single centered column at all sizes. On phones it spans the viewport with 16px side padding; on larger screens it caps at 640px and centers, with the page background extending edge to edge so a wide window doesn't look empty. Answer cards always stack vertically; there is no side-by-side mode at any width, because stacked anonymous cards are the design, not a mobile compromise.

Requirements:

- Works from 320px wide up with no horizontal scroll at any width.
- All tap targets at least 44px tall.
- Type scales with clamp(): body between 16px and 18px, question heading between 20px and 24px.
- No fixed heights anywhere; content defines height and the page scrolls naturally, so a short wide MacBook Chrome window (for example 1440 × 500 after a resize) shows the top of the round and scrolls, rather than clipping or squashing.
- Layout uses the small viewport unit (svh) where a viewport reference is needed, to avoid mobile browser chrome jumps; nothing critical depends on viewport height.
- Both orientations work; rotation mid-round loses nothing.
- Dark mode follows prefers-color-scheme.
- Resizing the window at any moment reflows without breaking state, because state lives in JS and layout is fluid.

Test matrix: iPhone SE (320 to 375px), current iPhone and Android widths, iPad portrait, MacBook Chrome full screen, MacBook Chrome at half width, and a deliberately awkward short-wide resize.

## Visual design

Clean, flat, and text-forward, closer to a well-designed reading app than a game. System font stack, generous line height, hairline borders on cards, one accent color used only for selection and primary buttons. The two answer cards are visually identical in every way except their content; any styling difference would leak the blind. Citation chips are small, muted, and part of the answer content, not the card chrome. No images, no icon fonts, no emoji, no confetti at the reveal; the reveal lands through copy and the tally, not decoration.

## Accessibility

Cards are real buttons with aria-pressed for selection state. Focus states are visible for keyboard play on desktop. The progress indicator and round transitions are announced via an aria-live region. Nothing is conveyed by color alone; the selected card also gets a checkmark and a border weight change. Reduced motion disables transitions. All text meets WCAG AA contrast in both color schemes.

## Technical constraints

One .html file containing all markup, CSS, and JavaScript inline. No frameworks, no build step, no external requests of any kind, including fonts, so the file works offline and inside restrictive networks. Target under 100KB total. All round content lives in one clearly commented JSON constant at the top of the script so questions and answers can be edited without touching game logic. No console errors in Chrome and Safari, desktop and iOS.

## Success criteria

1. First-time players on a phone finish in under 5 minutes without instructions beyond the intro.
2. In playtesting, a majority of players pick the unified assistant in at least 4 of rounds 3 through 7 without knowing the setup.
3. Asked afterward what the game was about, playtesters describe the data difference, not the AI difference.
4. Reload mid-game resumes correctly on iOS Safari, Android Chrome, and desktop Chrome.
5. The file opens and is playable from a direct file:// open, an email attachment, and any static host.

## Out of scope for v1

Live AI-generated answers, sharing or export of results, sound, localization, more than one question set, difficulty settings, and any server component. A v2 could add a second question pack and a share card, but nothing in v1 depends on them.
