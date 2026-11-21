# 🏠 Roomie: Tinder for Roommates

> **Swipe real-life scenarios, match with roommates who live like you, then chat.**

**Live app:** https://alpazder.github.io/roomie-hackjam/

A roommate questionnaire gets polite answers ("I'm pretty clean"). Roomie asks how you'd react to ten everyday moments instead, like dishes left overnight or friends over on a weeknight. It compares your answers with other students' and ranks who fits. When two people like each other, they match and can message.

---

## ✨ What it does

- **Real accounts:** sign up with email and password, log in from any device, reset your password.
- **Required onboarding:** Account → Profile → Your vibe (10 scenarios) → Discover. Progress saves to your account, so you can leave and resume.
- **Four-way scenario swipes:**
  - ➡️ Works for me
  - ⬅️ Not for me
  - ⬆️ **Dealbreaker** (counts 3×)
  - ⬇️ I'm flexible
- **Ranked discovery:** other students who finished onboarding, sorted by preference similarity. Change an answer and the ranking updates.
- **Mutual matching:** likes stay private. A match happens only when both people like each other.
- **Real-time chat:** messaging unlocks after a match. Includes unread badges and message history on any device, plus **Unmatch** and **Block**.
- **Profiles:** name, university, major, budget, city and move-in date (required); bio, interests and photo (optional). You can hide yourself from discovery.
- **Demo mode:** "Try demo mode" on the login screen shows four sample profiles with simulated likes, for anyone who doesn't want to sign up. Sample data is clearly labeled.

---

## 🧠 How compatibility works

Each scenario maps to one area: **cleanliness, sleep, guests, or pets**. Your answers become a 0–1 preference for each area, and we compare yours with the other person's, plus both monthly budgets.

- An area counts only if **both** people answered it. Missing areas are listed, never guessed.
- An area where either person chose **Dealbreaker** counts **3×**.
- The % is **preference similarity**, not a prediction that living together will work.

---

## 🛠 Tech stack

| Layer | Tools |
|---|---|
| Frontend | React, Vite, Tailwind CSS, Lucide icons |
| Accounts & data | Supabase Auth, PostgreSQL with Row Level Security, private photo storage, Realtime messages |
| Hosting | GitHub Pages (single self-contained `index.html`) |
| Optional AI | Google Gemini via Vercel server functions (`@google/genai`). **Not enabled on the live site**, see below |

**Security:** every table uses Row Level Security. Likes, matches, messages and blocks can only be written through database functions that check the signed-in user. Incoming likes stay private, and nobody outside a match can read or send its messages. The frontend only holds the Supabase **publishable** key.

---

## 📁 Repository layout

```
index.html          ← the live app (built from the source)
index2.html         ← redirects to the main app (old link)
roomie-source.zip   ← full editable source code
SETUP.md            ← Supabase setup, two-account test checklist, deploy steps
PRESENTATION.md     ← two-minute pitch script
```

Inside `roomie-source.zip`:

```
src/
  main.jsx, ScenarioLab.jsx, scenarios.js   app shell, scenario swipes, scoring
  real/                                     Supabase mode: auth, onboarding, profile, discover, matches, chat
  MatchChat.jsx, aiClient.js, aiFallbacks.js   demo-mode match chat + Gemini client with fallbacks
  BasicAuth.jsx, localUsers.js              browser-only login (used only when Supabase isn't configured)
  AiAdvice.jsx                              optional "Ask Gemini" coach (demo mode)
api/                 Vercel functions: gemini.js, explain.js, agreement.js, chat.js (+ _lib/gemini.js)
supabase/migrations/ database schema, security rules, functions, photo storage
tests/               database rule tests (tests/db) and browser tests (tests/ui)
scripts/package-pages.py   builds the single-file index.html for GitHub Pages
```

---

## 🚀 Run it locally

```bash
unzip roomie-source.zip -d roomie && cd roomie
npm install
cp env.example .env.local   # add your Supabase project URL + publishable key
npm run dev                 # http://127.0.0.1:5173
```

Without Supabase settings, the app falls back to a browser-only demo login.

**Database setup:** run the two files in `supabase/migrations/` in the Supabase SQL Editor. Full steps are in **SETUP.md**.

**Update the live site:**

```bash
npm run build                    # with .env.local filled in
python3 scripts/package-pages.py # writes /tmp/roomie-publish/index.html
```

Commit that file as `index.html`.

---

## 🤖 Google Gemini: status

Gemini is **integrated in the code but not running on the live site.** GitHub Pages can't run server code, so these features show labeled offline samples ("Offline sample · Gemini not connected"):

- **Demo mode → "Ask Gemini"** coach in the compatibility details (`api/gemini.js`)
- **Demo mode → Match Chat:**
  - a match breakdown with strengths, friction and an icebreaker (`api/explain.js`)
  - in-character replies from sample profiles, labeled AI-simulated (`api/chat.js`)
  - a Roommate Agreement (`api/agreement.js`)

Real-user accounts, scoring and chat between people **do not use Gemini.**

**To enable it:**
1. Deploy the source to Vercel (`npx vercel`).
2. Add `GEMINI_API_KEY` (and optionally `GEMINI_MODEL`) as server environment variables.
3. Build the frontend with `VITE_API_BASE=https://<your-app>.vercel.app`.

Every route has an 8-second timeout and a fallback. Don't claim live Gemini until a reply shows **"Written by Gemini"**.

---

## ✅ Testing

- `npm test`: 24 tests covering scoring, two-user compatibility, and the Gemini routes against a mock Gemini server (timeouts, errors, bad output, missing key, input limits, origin blocking).
- `npm run test:db`: 11 tests run the real migrations on PostgreSQL. They check onboarding rules, private likes, exactly one match even with simultaneous likes, message privacy (a third account is locked out), duplicate-free retries, unread state, unmatch/block, and photo access.
- `tests/ui/`: browser runs of every screen on desktop and mobile, using a fake backend.
- The team also ran live checks on the Supabase project for discovery, matching, chat privacy and unmatching.

---

## ⚠️ Known limitations

- **Email limits:** Supabase's built-in email sender allows about **2 emails per hour** for the whole project, and may only deliver to project team members. Extra sign-ups fail with *"email rate limit exceeded."* For a demo, turn off **Confirm email** (Authentication → Providers → Email). For real use, set up custom SMTP.
- **No student verification**, moderation, reporting or push notifications yet.
- **Accounts from the earlier browser-only login don't carry over.** Sign up again.
- **Discovery shows answers:** people in discovery can see each other's scenario answers, which are needed to compute similarity.

---

## 🔮 What's next

- Custom SMTP and student-email verification (e.g. `@usf.edu`)
- Live Gemini on Vercel for match breakdowns and agreement drafts (never for writing messages between real people)
- Reporting and moderation tools
- Group matching for 3–4 person apartments
- Partnering with campus housing and nearby listings
