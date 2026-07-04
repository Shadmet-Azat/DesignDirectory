# Community data — what people actually name as "AI design"

The evidence behind this skill's bans, so they stay grounded in observation, not one person's taste.
Source: a public 3.2M-post Reddit study (JCarterJohnson/vibecoded-design-tells, mined from the free Arctic
Shift archive, 47 AI/SaaS subreddits, 2020–2026; comment-level ranking is the clean signal — those threads are
100% on-topic; 11 of 12 top tells adversarially verified). Trust the *relative order*, not exact percentages.

## Ranked tells (share of on-topic comments)
| tell | % | note |
|---|---|---|
| **"Screams AI" / soulless / slop** (meta) | 6.4 | the loudest complaint is *sameness itself*, not any one element |
| **"All looks the same" / template** (meta) | 6.1 | → the skill's job is to break the *lane*, not just swap elements |
| shadcn / Tailwind default kit ("slate cards + that blue accent + same padding") | 2.5 | **#1 concrete tell** — the cobalt-on-white miss is this |
| "AI purple" / violet (Tailwind indigo) | 2.3 | |
| gradients everywhere / gradient hero text | 2.0 | highest-upvoted single design comment |
| too many animations / Framer fade-ins | 1.1 | |
| rounded corners / pill buttons | 0.8 | validates the pill ban |
| dark mode + neon glow | 0.7 | dark mode itself is fine; only *unprompted glow* is the tell |
| emoji / ✨ / 🚀 in copy | 0.5 | |
| generic sans (Inter / Geist) | 0.4 | |
| three-column feature cards | 0.4 | |
| mesh / blob / **aurora** backgrounds | 0.3 | **rejected as a keyword artifact — NOT a real tell** |
| glassmorphism | 0.2 | low + contested |
| same hero: huge headline + 2 buttons | 0.2 | |
| centered everything / endless whitespace | 0.2 | |
| **bento grid** | 0.1 | **near-bottom — a Twitter meme, not a real complaint** |

## The two corrections this forces on the skill
1. **Don't headline bento / glassmorphism / aurora as tells.** The data ranks them at the bottom or rejects
   them — they're "Twitter memes" people *repeat* but don't actually flag in the wild. Over-flagging trains
   people to ignore the tool. (The menu's fatigue index is corrected accordingly.)
2. **The coarse layer is owned upstream.** Everything in the table above is the *coarse* layer — already
   covered by unslop-ui (data-ranked + a CI scanner), taste §7, impeccable. This skill does NOT restate it.

## The coarse / subtle split (why this skill exists)
The corpus only contains tells *laypeople can name*. The skill's own §1 bans — italic accent-word headline,
letter-spaced eyebrow + status dot, middot stat row, divider-CTA, fake-technical decoration — **do not appear
in the data** because regular Redditors can't articulate them; they just feel "off." But *designers* name them
(the community `performativeUI` parody catalog ships them as components: `EyebrowPill`, `GradientText`,
`StatusDot`, `WordRoll`, `Sparkle`). So this skill's non-duplicative lane is the **subtle, designer-resolution
layer above the corpus's resolution**, plus the technique vocabulary. The coarse bans stay upstream.

## The shared trap (independently confirmed)
Both this skill (bake-off round 2) and unslop-ui hit the same wall: **replacing one default look with another.**
The current "tasteful default" — cream + serif display (Instrument Serif/Fraunces) + sage/forest green — is the
Claude/Anthropic house style and reads as a tell just as fast. The rut-breaker's named-offender list exists to
stop that lateral move.