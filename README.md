# Roomie — Tinder for Roommates

A React + Vite hackathon MVP with responsive purple-and-white styling, touch swiping, profile onboarding/editing, five-factor compatibility, simulated mutual matches, an undo action, filters, and local persistence.

## Start on your Mac

Dependencies are already installed in this folder.

```sh
cd /Users/alp/Documents/Codex/2026-11-21/new-chat/roomie
npm run dev
```

Open the localhost URL printed by Vite. For mobile testing on the same trusted Wi-Fi, run `npm run dev -- --host 0.0.0.0` and open the Network URL on your phone.

## Build and test

```sh
npm test
npm run build
npm run preview
```

Four Node tests cover identical profiles, all five score bounds, budget differences, and explanation attribution. The production build passes. Desktop and 390px mobile rendering have been checked in Chrome. A like triggered the mutual-match notification and saved Maya on the matches page.

## Vercel deployment

From this project directory in your Mac Terminal:

```sh
npx vercel login
npx vercel --prod
```

Choose your account, create a new project named `roomie-hackjam`, retain this directory, and use the detected Vite configuration. Build command: `npm run build`; output: `dist`. No environment variables are needed. Never paste tokens into the code. Open the returned public URL and verify it before presenting.

## Architecture

- `src/main.jsx`: React UI, navigation, onboarding/profile form, swipe gestures, filters, match dialog, and compatibility detail view.
- `src/compatibility.js`: equal-weight compatibility across budget, cleanliness, sleep schedule, guests, and pets. Budget similarity falls by one point per $8 difference, clamped at zero; categorical preferences use transparent fixed scores. Scores indicate preference similarity, not a validated prediction.
- `src/data.js`: five fictional student profiles. `likedYou` flags simulate incoming likes; a like becomes a match only if both sides like one another.
- `src/style.css`: Tailwind CSS utilities plus custom responsive CSS, mobile bottom navigation, animated swipe transitions, and reduced-motion support.
- `localStorage`: saves the current profile, swipe history, and matches in this browser. There are no real accounts, messaging, shared database, or student verification.
- Images: remote Unsplash portraits with colored initial-avatar fallbacks; Google Fonts have system fallbacks. All profiles are fictional.

## Current scope and limits

React and Vite are installed. Tailwind is not installed: the registry was unavailable and Tailwind was not cached. The interface uses custom CSS so the project still builds. To add Tailwind when internet access is available:

```sh
npm install -D tailwindcss @tailwindcss/vite
```

Then add `tailwindcss()` from `@tailwindcss/vite` to Vite's plugins and `@import "tailwindcss";` at the beginning of the CSS file. No Gemini API call is included; compatibility explanations are deterministic and explicitly labeled. Gemini would need a server-side endpoint with its key stored in Vercel environment variables. Supabase is deferred.

## Demo walkthrough

1. Create a profile with your name, university, budget, and habits.
2. Tap the compatibility badge to see five-factor reasoning.
3. Like Maya to trigger a guaranteed simulated mutual match.
4. Open Your matches and inspect the breakdown.
5. Edit your profile to show compatibility changing.
6. Explore budget and university filters; use undo to revisit a swipe.

## Team of three

1. UI owner: discovery card, responsive layout, accessibility, visual polish.
2. Logic owner: scoring, onboarding, local storage, tests.
3. Demo/deployment owner: Vercel, judge walkthrough, presentation, mobile verification.

## Remaining acceptance checks

- Create and edit profile; reload to verify persistence.
- Pass, like, swipe by touch, undo, and exhaust all profiles.
- Confirm a like on Maya matches; a like on Alex does not.
- Test filters with no results and clear them.
- Check 390px mobile and desktop layouts, keyboard access, and console errors.
- Deploy, open the public URL, and repeat the main walkthrough.
