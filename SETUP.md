# Roomie real-user mode: setup, test, deploy

Takes about 15 minutes. Nothing here needs a secret pasted into chat or code: only the
project URL and the **publishable** key go into the frontend.

## 1. Create the Supabase project
1. https://supabase.com/dashboard → **New project**. Pick a region near Florida (e.g. East US). Save the database password in your password manager.
2. **Project Settings → API**: copy the **Project URL** and the **publishable key** (`sb_publishable_…`, or the legacy `anon` key).

## 2. Run the migrations (in order)
Either:
- **SQL Editor** → New query → paste `supabase/migrations/20261121000001_roomie_core.sql` → **Run**. Then the same for `20261121000002_roomie_storage.sql`.

or, with the CLI:
```sh
npx supabase login
npx supabase link --project-ref YOUR-PROJECT-REF
npx supabase db push
```

Check afterwards:
- **Database → Publications → supabase_realtime** includes `messages` (the migration adds it; toggle it on if not).
- **Storage** shows a private `avatars` bucket (3 MB, JPEG/PNG/WebP).

## 3. Auth settings
**Authentication → URL Configuration**
- Site URL: your deployed URL (e.g. `https://roomie-hackjam.vercel.app`).
- Redirect URLs: add the deployed URL, `http://localhost:5173`, and `http://127.0.0.1:5173`.

**Authentication → Providers → Email**
- Leave **Confirm email** on for real use. For a quick two-account test with throwaway addresses you can turn it off temporarily, then turn it back on.
- Set minimum password length to 8 (the app asks for 8).
- Supabase's built-in email sender has a low hourly limit. For a public launch, add custom SMTP (**Authentication → Emails → SMTP Settings**).

## 4. Run locally
```sh
cp env.example .env.local      # then fill in the URL and publishable key
npm install
npm run dev                    # http://127.0.0.1:5173
```
Use two browsers (or a normal and a private window) for two accounts.

## 5. Two-account acceptance checklist
- [ ] a. A signs up and completes Profile → Your vibe (10 scenarios) → lands on Discover.
- [ ] b. Mid-onboarding, refresh: it resumes on the same step/answer.
- [ ] c. B signs up and finishes onboarding. A and B each see the other in Discover.
- [ ] d. A likes B. No match appears for either. B's screen shows no hint of A's like.
- [ ] e. B likes A. Both get the match. **Table Editor → matches** has exactly one row for the pair.
- [ ] f. Exchange messages; they appear on the other screen without refreshing.
- [ ] g. Refresh both: history is still there; unread badge counts are right and clear after opening.
- [ ] h. Create account C. C never sees A/B's conversation, and the Matches page is empty.
- [ ] i. A blocks B (or unmatches): the conversation closes for both, sending fails, and neither appears in the other's Discover.
- [ ] Check desktop and a phone (or the browser's mobile view).

Database rules can also be re-tested any time against a local PostgreSQL:
```sh
DATABASE_URL=postgres://postgres@localhost:5432/postgres npm run test:db
```

## 6. Deploy (Vercel recommended)
```sh
npx vercel login
npx vercel                      # first deploy creates a preview URL
```
In the Vercel dashboard → Project → **Settings → Environment Variables**, add
`VITE_SUPABASE_URL` and `VITE_SUPABASE_PUBLISHABLE_KEY`, then:
```sh
npx vercel --prod
```
Add the production URL to Supabase's Site URL and Redirect URLs (step 3), and run the checklist again on the public URL.

## 7. Replacing the GitHub Pages site (only after step 6 passes)
The current Pages site keeps working untouched. When you're ready:
```sh
npm run build                   # with .env.local filled in, so the build contains the URL + publishable key
python3 scripts/package-pages.py
```
Commit `/tmp/roomie-publish/index.html` as the repo's `index.html`, then add
`https://alpazder.github.io/roomie-hackjam/` to Supabase's Redirect URLs.
Without Supabase variables, the build runs in demo mode only, same as today.

## Gemini
Still **not enabled**. Real-user mode doesn't call Gemini. Don't claim Gemini features until a live request has been verified (see README).
