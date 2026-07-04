---
name: design-techniques
description: >
  The technique-vocabulary layer for visual/frontend work — load it during the build step when the brief is
  aesthetic but under-specified ("make it beautiful / cool / modern", "add some animation", "spice up this hero",
  "build me a landing page"). It NAMES the distinctive frontend TECHNIQUES a layperson doesn't know to ask for
  (scroll-scrub, kinetic type, magnetic, custom cursor, view transitions, mesh/shader gradient, clip-wipe,
  scrollytelling, grain…) and carries each one's award-tier-vs-generic implementation PARAMS — so the model reaches
  past its median default and hits real craft, with or without a dedicated builder skill. It SELF-ROUTES by
  buildability (easy-plain-CSS / needs-library / builder-assisted) so it fits a plain build, not only an expert one.
  Runs UNDER context-engineering (which brings the brief, taste, and research — don't re-ask). Builder-agnostic: load
  it alongside frontend-design / GSAP / motion, or use its params to drive a direct build. It is NOT a concept
  generator and NOT a tell-remover. Skip for non-visual work, or when the brief already names its techniques.
---

# Design Techniques — name the move, then build it to award-tier params

## What this is (read first)
A layperson types "make it beautiful" and gets slop — not because the model lacks ability, but because **no explicit
decision got made, so it shipped the default, and the default is the median, identical for everyone.** (That's the
community's own diagnosis, not ours: *"the AI look is what happens when no explicit decision gets made — you ship the
defaults, you get the median."*) They didn't know to ask for scroll-scrub, a magnetic CTA, or a layered zoom.
**This skill supplies the named moves they couldn't specify, and the real parameters that separate a crafted
implementation from a recognisable one.**

It is **additive — name the move you didn't know**, not "scrub the AI tells off." A removal framing both reads as
masking *("a cover-your-tracks tool")* and only ever reaches the floor. Avoiding tells is necessary and radically
insufficient for greatness; the job is the ceiling.

## Where it sits — builder-agnostic, NOT FD-dependent
- **context-engineering** (above) brings the brief, the taste elicitation, the research + Quality Reference Filter.
  **Assume that context arrived. Don't re-run questions or research here.**
- **design-direction** (the prior-axis partner, when present) hands down a **DIRECTION BRIEF** — `genre/mood ·
  type · palette · composition · signature · assets (prompts + layer plan) · stakes`. This skill is the **execution
  half**: realize that direction's *signature* with the fittest techniques, compose its assets (incl. the layer
  plan), and honor its `stakes` (the purpose-calibration — don't over-build a utility). Together the two **replace
  frontend-design**. Don't restate direction/type/color here — that's design-direction's job.
- **This skill** = the technique vocabulary + the fit/selection judgment + the award-tier params. It carries enough
  craft to lift a build *on its own* (no direction skill required).
- **Builders** — frontend-design (craft/aesthetic execution) · GSAP/ScrollTrigger · motion/framer-motion (correct
  motion) — are *optional executors*. Load this skill **alongside** one when present, **or** use its params to drive a
  direct build. The value lives where a generic build is weak (the named move + the real params), so it is neither
  redundant with a strong builder nor dependent on one.

---

## The core — select the fittest move, build it to its award-tier params

### 1 · Anchor on the one differentiator + the brief's stakes
One line: the subject's single most *real*, differentiating fact, and what the brief actually wants (a showpiece, or a
utility that must stay out of the way?). This is the **selection input** — not a concept to generate. If nothing is
distinctive, say so and reach for a brand/editorial/emotional angle; don't invent a fake spec to decorate.

### 2 · Select 1–2 techniques that FIT — filtered by buildability (load `references/technique-menu.md`)
Pick the **1–2 that fit the brief's *intent* — the fittest, not the flashiest.** Then **filter by who is building** (the
menu's buildability index):
- **No expert builder / a layperson ships this** → restrict to **`easy-plain-CSS`** and **`needs-library`** techniques;
  **gate `builder-assisted-only` behind an explicit "this needs a developer."** Those fail ugly without a hand-built
  fallback (the WebGPU trap — "rising on the platform" ≠ "fittest for a non-designer to ship").
- **A capable builder is in the loop** → the full menu is open.

Read each entry's **"when it's slop"** guard before committing. One or two moves, never a buffet.

### 3 · Build to the award-tier params — a craft axis, not a checklist
For each chosen technique, apply its **award-tier-vs-generic** field (the menu's 5th field): the real parameters that
separate crafted from recognisable — e.g. *magnetic = ~0.3 fractional pull from a padded zone, not a 1:1 teleport*;
*grain = a `fractalNoise` SVG at 3–8% over `soft-light`, not a 15 % dark PNG*. Treat these as **an axis to move along
for THIS subject**, never a fixed configuration to satisfy. A checklist ("always 4 mesh stops, always a dual-layer
cursor") just manufactures a louder, more expensive default — the exact wall the sibling anti-slop skills hit
("Specimen fall-through"). If a builder skill is active, hand it the technique + params **by name**; if you're building
directly, you already have them.

### 4 · Change structure, not just surface
The new-uniform trap is *surface-swapping*. **"The same shadcn app but green with a serif font" is NOT a break** — the
community names this exactly. A real move changes **form / layout / interaction** — which is why this skill's lane is
*techniques*, not a palette. A font + colour change never counts as the differentiator.

### 5 · Floor cleanup — last, brief, optional (demoted)
*Only after the move is set*, a ~60-second pass so the bold thing doesn't carry an accidental cliché:
- **Coarse mechanical tells** (shadcn default kit, AI-purple/blue gradient, gradient hero text, unprompted neon glow,
  emoji-as-icons, Inter/Geist) → **defer to unslop-ui / taste §7.** Don't restate them.
- **Subtle residual tells** a strong build still emits — *kill the tell, keep its function:* italic accent-word
  headline · letter-spaced uppercase eyebrow + status dot · middot stat row · fake-technical decoration
  (`REV A`, `FIG.01`, invented part numbers) · the divider-CTA (`Pre-order │ $219`).
> **If you spent more effort here than on steps 2–3, you inverted the skill.**

---

## Guardrails
- **Layout-first (lo-fi before hi-fi)** — when the build involves real imagery or a non-trivial composition, build and
  get a **greybox wireframe** approved (real text + type/hierarchy, grey placeholder boxes for image zones, no real
  assets) BEFORE compositing assets. Focal point, hierarchy, and text legibility are cheapest to fix in grey boxes;
  discovering them after assets are generated and composited is the expensive path. Preview text on the value it will
  sit on (light-on-dark vs ink-on-paper), so a pale accent over a busy image is caught now, not after.
- **One or two signature moves, not a buffet** — one memorable technique done well beats six competing.
- **Motion must be perceptible and purposeful** — an 80s rotation is invisible; a fade on everything is noise. And it must MEAN, not just move — the
  signature motion reproduces a real phenomenon of the subject (coffee blooms, ink bleeds), not an abstract tween
  bolted on. Making it *move* is not making it *mean*.
- **Alive on arrival (showpiece)** — budget an entrance + a touch of ambient motion on first paint; motion gated
  entirely behind hover/scroll leaves a dead screen at rest. Restraint = one bold idea, not stillness. Calibrate to
  stakes — a utility stays quiet.
- **No information theater** — a device that implies data (legend, axis, gauge, contour, measurement) must carry the
  real key/data, or be cut. Don't build a device whose only job is to *look* informative.
- **Match the move to the stakes** — a WebGL cursor on a utility dashboard is inverse slop (impressive and wrong).
  Honor the counter-signal: for a utility a layperson ships, real users *don't care* about "tells" — don't force a
  showpiece move onto a surface that doesn't want one.
- **Floor of perf/accessibility** — honour `prefers-reduced-motion`, give a mobile/touch fallback, and name a heavy
  technique's cost (load, input latency, Lenis/scroll-jacking vs assistive tech) in the same breath you propose it.

## Ship check — screenshot the render and judge it (quality, not just bugs)
**Never present a visual build you haven't SEEN.** After building, render it at desktop AND mobile (headless
screenshot or a devtools MCP) and look at it as a harsh art director. The bar is *"is this genuinely beautiful and
world-class?"* — not merely *"does it run without bugs?"*. Catch the weaknesses and FIX them yourself before showing
anyone:
- **Beauty / quality** — premium or just competent? A clear focal point + reading path? Is the type hierarchy doing
  real work? Does the signature actually land, or is it inert?
- **Legibility** — read every text over its *real* background (light-on-dark vs ink-on-paper). A pale accent lost on
  a busy image, or low-contrast body copy, is a defect — not a nuance.
- **Craft bugs** — collisions, overflow, edge-clipping, broken responsive, elements the entrance stranded off-state.
Fix → re-screenshot → repeat until it holds. Handing off flawed work with "this needs polish" caveats for the
reviewer to catch is the failure — you can see it, so fix it. This is a QUALITY loop, not just a bug sweep.

## References
- `references/technique-menu.md` — ~40 named techniques in 8 families (what · when-fits · when-slop · how-built ·
  award-tier-vs-generic), each routed by a **buildability index** (`easy-plain-CSS` / `needs-library` /
  `builder-assisted-only`) + a tool→technique map + the fatigue index. **The constructive core — load it at step 2.**
- `references/community-data.md` — the ranked evidence behind the tells + the coarse/subtle split. Keeps the cleanup
  grounded in observation, not one person's taste.