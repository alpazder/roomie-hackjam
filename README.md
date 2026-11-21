# Roomie — scenario-based roommate discovery

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
