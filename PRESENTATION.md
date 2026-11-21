# Roomie — Two-minute presentation

**0:00–0:20 — The problem**
“Your roommate affects your sleep, your budget, and whether home feels like home. A photo and ‘I’m pretty clean’ don’t tell you how your everyday habits fit. Roomie helps students compare concrete living preferences before moving in.”

**0:20–0:50 — Show the scenarios**
[Open Your vibe.]
“Every account completes a profile and ten everyday scenarios. Dishes left until tomorrow: left means not for me, right means works for me, up means dealbreaker, and down means flexible. A dealbreaker counts three times as much. These are self-reported choices; we don’t claim to detect honesty.”

**0:50–1:15 — Show discovery**
[Open Discover and the similarity breakdown.]
“We compare both people’s answers across cleanliness, sleep, guests, and pets, plus their budgets. Edit your answers and the ranking recalculates. The percentage measures preference similarity, not a guarantee that living together will work. Real profiles enter this pool only after completing onboarding.”

**1:15–1:40 — Show a match and chat**
[Use two prepared accounts with completed onboarding, or explicitly announce sample demo mode.]
“Likes stay private until they’re mutual. When both people like each other, Roomie creates a match and unlocks private messaging. You can ask about daily routines, see unread messages, and unmatch or block to close the conversation.”

**1:40–2:00 — Architecture and next steps**
“Our frontend uses React, Vite, and Tailwind. Supabase handles accounts, saved profiles, private photos, and messages. Database rules enforce ownership, onboarding, and match membership. We tested the scoring and live database permissions, plus the mobile interface. Student verification and moderation are next. Gemini server routes are prepared, but live Gemini is not enabled on this GitHub Pages deployment.”

## Demo preparation

Resolve email delivery before inviting public accounts. Prepare two confirmed accounts and complete all ten scenarios on each; use separate browser profiles for a live mutual-match demonstration. If using Try demo mode, say clearly that profiles and replies are samples. Do not call offline fallback advice Gemini output.
