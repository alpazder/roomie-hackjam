# Roomie — Two-minute pitch

**0:00–0:20 — Problem**
“Finding a roommate means choosing someone you’ll share everyday life with. A photo, a major, and ‘I’m pretty clean’ don’t tell the whole story. Roomie helps students explore concrete living situations and discover which preferences matter most to them.”

**0:20–0:55 — Four directions**
[Open Your vibe.]
“Instead of selecting cleanliness and sleep labels, react to situations. Your roommate leaves dishes until tomorrow. Do you swipe right because it works for you, left because it doesn’t, up because it’s a dealbreaker, or down because you’re flexible?”
[Mark the dishes card as a dealbreaker.]
“That answer immediately updates our numerical preference model. A dealbreaker carries more weight than a mild dislike. This is learning from self-reported choices—not a lie detector.”

**0:55–1:20 — Visible adaptation**
[Point at the live ranking. Answer another card.]
“The roommate list re-ranks after each answer. The fingerprint shows which dimensions you’ve explored. Undo an answer and the model recalculates. Before you answer any scenarios, we show no lifestyle percentage. Once you do, the score is an early preference-similarity estimate, not a promise that living together will work.”

**1:20–1:40 — Discover and connect**
[Open Discover, inspect the fit badge, and like a profile.]
“Discovery uses that same ranking. You can see the factors behind the score, like a profile, and get a simulated mutual match. These named profiles use sample lifestyle preferences; our demo does not claim those are facts about the people.”

**1:40–2:00 — Build and next steps**
“We built Roomie in React, Vite, and Tailwind, with browser-local persistence and tested scoring logic. The published GitHub Pages demo works without an account. Real student verification, messaging, and shared data come next. We’ve prepared a server-side Gemini integration, but it is not enabled in this deployment. We won’t claim AI-generated advice until a real request succeeds.”

## Optional Gemini segment — only after live validation

Replace the final paragraph only after the deployed endpoint returns a genuine response and the UI labels it Gemini. Show the response live. Explain that Gemini suggests discussion points using anonymous scenario choices; the numerical rank remains a separate transparent model. Do not claim Gemini-track eligibility based on an unconfigured endpoint.
