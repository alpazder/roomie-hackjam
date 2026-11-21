# Roomie — scenario-based roommate discovery

## Match chat + Gemini (new)

- **Match popup:** a Gemini match breakdown (3 strengths, 1 friction point with a suggestion, an icebreaker) plus a **Message [name]** button.
- **Messages** (header button with a match count, or the Your matches tab): matches list with fit %, last message and unread badge. Opens a chat with a typing indicator, a one-tap **Send Icebreaker** chip, and a **Roommate Agreement** button (Gemini, rendered with react-markdown).
- **Sample profiles:** Gemini replies *in character*, clearly labeled "Sample profile · replies are AI-simulated". **Real accounts** (same browser) get real person-to-person messages, never AI.
- Chats, matches and match breakdowns are saved in localStorage per account, keyed by profile id.

Server routes (Vercel functions, key stays on the server): `api/explain.js`, `api/agreement.js`, `api/chat.js`, sharing `api/_lib/gemini.js` (`@google/genai`, model `GEMINI_MODEL` or `gemini-2.5-flash`, **8 s timeout**, input limits, the same origin allowlist as `api/gemini.js`). Every route returns a hard-coded fallback on a missing key, timeout, error or bad JSON, and the browser falls back too if there's no server at all (e.g. GitHub Pages). Fallback text is always labeled "Offline sample · Gemini not connected".

**To turn Gemini on:** deploy the source to Vercel (`npx vercel`), add `GEMINI_API_KEY` (and optionally `GEMINI_MODEL`) under Settings → Environment Variables, then `npx vercel --prod`. Open the Vercel URL. To keep using GitHub Pages for the frontend, build with `VITE_API_BASE=https://<your-vercel-app>.vercel.app` and add the Pages origin to `ROOMIE_ALLOWED_ORIGINS` if it isn't `https://alpazder.github.io`.

Tested: `api/ai.test.js` (6 tests against a mock Gemini server: JSON mode, server key, in-character prompt, fallbacks for bad JSON, errors, timeout and a missing key, input limits, origin blocking) and `tests/ui/chat_smoke.py` (match → breakdown → chat → icebreaker → AI reply → agreement → reload, with and without the API). **Not yet verified against the live Gemini API.** Check a real reply shows "Written by Gemini" before pitching it.

## Basic login (no Supabase)

Without Supabase settings, the app opens on a simple **Log in / Sign up** screen. Accounts, profiles, scenario answers, swipes and matches are saved per account in this browser's localStorage. There's no encryption or real security, so don't use real passwords. Code: `src/BasicAuth.jsx`.

## Real-user mode (Supabase), new

When `VITE_SUPABASE_URL` and `VITE_SUPABASE_PUBLISHABLE_KEY` are set, Roomie runs as a multi-user app. Without them it runs in the labeled demo mode below. **Setup, testing and deployment steps: see [SETUP.md](SETUP.md).**

- **Accounts:** Supabase Auth email/password sign-up, login, logout, password reset and persistent sessions. Emails stay in `auth.users` and are never shown to other users.
- **Required onboarding:** Account → Profile → Your vibe (all 10 scenarios) → Discover. Progress is saved to the account (`profiles.onboarding_step` plus one row per answer), so it resumes after a refresh or on another device. The database refuses to advance a step until it's complete.
- **Profiles:** display name, university, major, budget, preferred city and move-in date are required. Bio, interests and photo are optional. Users can choose whether they appear in discovery. Photos go in a private bucket (JPEG/PNG/WebP, 3 MB), are re-encoded in the browser to strip location metadata, and can be viewed only by signed-in users allowed to see that profile.
- **Compatibility between two real users** (`fitUsers` in `src/scenarios.js`) uses both people's scenario answers and both budgets. An area counts only if both people answered it; dealbreakers count 3×; missing areas are listed and never guessed. It measures preference similarity, not a prediction.
- **Matching:** `record_swipe()` stores likes and passes, and creates a match only when both people liked each other. A per-pair lock plus a unique constraint mean simultaneous likes produce exactly one match. Incoming likes are invisible until mutual. There are no simulated likes in real mode.
- **Messaging:** only between matched users. `send_message()` checks membership, the match being open and blocks, and a trigger re-checks on every insert. Message ids come from the client, so retrying a failed send never duplicates it. Realtime delivery, 30-message pages, timestamps, unread counts (`conversation_reads`), plain-text rendering, and unmatch/block, which closes the chat and removes the pair from each other's discovery.
- **Security:** Row Level Security on every table. Swipes, matches, messages and blocks have no direct write policies (writes go only through checked database functions). Helper functions aren't executable by clients. Only the publishable key is used in the frontend.

Code: `src/real/` (UI and data layer), `supabase/migrations/` (schema, RLS, functions, storage), `tests/db/` (database rule tests on PostgreSQL), `tests/ui/` (UI smoke test with an in-memory fake backend).

### Tested so far
- `npm run test:db`: 11 tests run the real migrations on PostgreSQL 16 (with a small Supabase stand-in for `auth`/`storage`) and act as several users: onboarding gates, profile/answer ownership, discovery eligibility, private one-sided likes, exactly one match (including 15 rounds of truly concurrent likes; the test fails if the pair lock is removed), messaging membership, idempotent retries, unread state, a third account locked out, unmatch/block, and photo access.
- `npm test`: scoring tests, including two-user compatibility and missing-answer handling.
- `tests/ui/smoke.py`: 20-step browser run of every screen against a fake backend (sign-in, validation, resume after refresh, undo, ranking, match, send/retry, realtime receive, unread, revisiting answers, discovery toggle, block, logout, demo mode), on desktop and mobile.
- **Not yet tested against a live Supabase project, Realtime or Storage.** Run the SETUP.md checklist before relying on it.

### Known limitations
- Scenario answers of people in discovery are visible to other signed-in users, since both sets are needed to compute compatibility.
- No student verification, content moderation, reporting, rate limiting beyond Supabase defaults, or push notifications.
- Supabase's built-in email sender is rate-limited; add custom SMTP before a public launch.
- The bundle is large (~1.7 MB) because the demo photos are inlined.


Live demo: https://alpazder.github.io/roomie-hackjam/

React, Vite, Tailwind CSS, and localStorage. The deployed GitHub Pages index is a self-contained static build; the source archive contains the editable application and optional API handler.

## Local setup

```sh
npm install
npm run dev
npm test
npm run build
```

On this Mac, the project is at `/Users/alp/Documents/Codex/2026-11-21/new-chat/roomie`.

## What works

- Ten real-life scenario cards, pointer swipes in four directions, keyboard arrow controls, and equivalent labeled buttons.
- Left: not for me; right: works for me; up: dealbreaker; down: flexible.
- A small online numerical preference model. Each dimension starts at a neutral 0.5 prior with weight 1. Answers add weighted evidence: yes/no 1, dealbreaker 3, flexible 0.25. A negative response inverts the scenario's target; flexibility contributes 0.5.
- Live ranking after every scenario answer, visible evidence counts, undo, reset, and a discovery list sorted with the same score.
- Budget similarity is `max(0, 1 - absolute budget difference / 1000)`. Answered lifestyle dimensions contribute `1 - absolute learned/candidate preference difference`. A dimension with a dealbreaker receives weight 3 instead of 1. Scores are weighted means, rounded to percentages.
- No fit percentage before any scenario evidence. Unexplored dimensions are labeled. These are self-reported preference estimates, not validated personality or tenancy predictions.
- Profile basics and budget editing; profile like/pass; simulated incoming likes and mutual matches; local persistence.
- Four requested named demo profiles. All lifestyle, budget, age, university, and interest values are sample data, not asserted facts about the people.

## Modules

- `src/scenarios.js`: scenarios, inference, ranking, score breakdown, transparent numerical explanations.
- `src/ScenarioLab.jsx`: four-way input and live ranking interface.
- `src/main.jsx`: navigation, profile form, discovery, and match flow.
- `src/data.js`: sample profiles.
- `src/AiAdvice.jsx`: optional live Gemini request with explicit loading/error/provider states.
- `api/gemini.js`: Vercel serverless handler, input validation, CORS, anonymous preference-only payload, server-held key, timeout and error handling.
- `scripts/package-pages.py`: creates the self-contained Pages index from the production build.

## Gemini status: NOT ENABLED

The static GitHub Pages deployment does not run server functions. Numerical explanations are not Gemini output, and no placeholder text is presented as AI. The Gemini integration code has not been tested against a live model/key.

To enable on Vercel:

1. Deploy the complete source project to Vercel with Vite defaults.
2. Add `GEMINI_API_KEY` as a server-only environment variable in the Vercel dashboard. Never use a `VITE_` prefix for the key and never commit it.
3. Add `GEMINI_MODEL` with a model currently supported by your Gemini account.
4. If retaining a separate GitHub Pages frontend, set `VITE_GEMINI_ENDPOINT` to the full HTTPS `/api/gemini` endpoint before rebuilding the frontend. This URL is public configuration, not a secret. The server permits `https://alpazder.github.io` plus origins explicitly set in `ROOMIE_ALLOWED_ORIGINS`.
5. Verify a successful live API response and the UI's Gemini provider label before claiming Gemini functionality or using an AI-track pitch. Enforce provider spending limits and appropriate rate limits before wider use.

Reference: https://ai.google.dev/api/generate-content

## Honest demo limitations

The choices are still self-reported. Scenario framing does not establish that responses are more honest. Ranking is a deterministic incremental model, not a trained ML system. Simulated matches do not contact real people. Names/photos are user supplied; sample preferences are invented. There are no real accounts, database, chat, student verification, or push notifications.

## Team work

- Developer 1: scenario cards, gestures, and mobile UX.
- Developer 2: preference model, score tests, and profile data.
- Developer 3: deployment, optional Gemini backend, and presentation validation.
