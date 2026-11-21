# Roomie — Tinder for Roommates

Live app: https://alpazder.github.io/roomie-hackjam/

## Published release — November 21, 2026

The main site uses Supabase Auth and the live Roomie database. Create an account, confirm your email, complete your profile and all ten scenarios, then enter the real roommate pool. Both users must like each other before messaging unlocks. The sample photo profiles are available separately through Try demo mode.

Frontend: React, Vite, Tailwind. Backend: Supabase Auth, PostgreSQL with RLS, private avatar storage, Realtime messages. Compatibility compares scenario answers and budgets; it does not use Gemini.

Download and extract roomie-source.zip for editable source. Run npm install, copy env.example to .env.local, enter your project URL and publishable key, then npm run dev. Never use a secret/service-role key in frontend configuration. See SETUP.md for database migrations and deployment.

Validation: 24 automated scoring/API tests passed; production build passed; live transaction tests passed for discovery, mutual matches, chat privacy, deduplicated sends, read state and unmatching. Browser checks covered profile → ten scenarios → discovery → match → chat using an isolated fake backend, including 390px mobile layout. A real email-confirmation/login round trip still needs an account owner to complete it. No test users or messages were retained in the live database.

The legacy index2.html now redirects to the main app. Old browser-only accounts do not become Supabase accounts; users must sign up again.

Supabase's default email service may restrict delivery to organization-member addresses. Configure custom SMTP for unrestricted public confirmation/reset emails before inviting a crowd. Email verification remains enabled.


## Optional Gemini demo routes

The source preserves the newer demo chat, match breakdown and roommate agreement features in src/MatchChat.jsx and api/. Live Gemini requires deploying the server functions to Vercel and setting GEMINI_API_KEY server-side. GitHub Pages cannot execute those routes; fallback content is labeled offline. API tests use a mock provider, not live Gemini. See env.example for optional public API-base configuration.
