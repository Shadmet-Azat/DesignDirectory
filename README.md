# DesignDirectory

A design-quality **skill system for Claude Code** that pushes AI-generated interfaces past the generic default toward award-tier craft.

Most AI-built frontends land on the same safe, templated look. This system fixes that in two layers: one decides **what a design should be**, the other supplies the **techniques to execute it well**. Together they name the specific aesthetic directions and frontend techniques a layperson (or a model reaching for its median default) wouldn't otherwise ask for.

---

## The two skills

### 1. [`design-direction`](skills/design-direction/) — decides *what the design should be*
The **prior-axis** half. It commits a build to a specific, named aesthetic direction instead of the templated default, anchored to the experiential top of the field.

- A vocabulary of named aesthetic **movements** a layperson can't ask for — Swiss, editorial, neobrutalism, maximalism, retro-futurism, light-skeuomorphism, organic, luxury, technical, cinematic, 3D-immersive, generative, and more ([`references/movements.md`](skills/design-direction/references/movements.md)).
- Art-directs bespoke **AI-generated assets** and emits the generation prompts ([`references/ai-asset-generation.md`](skills/design-direction/references/ai-asset-generation.md)).
- A growing library of **learned references** distilled from real award-tier sites into reusable principles ([`references/learned-references.md`](skills/design-direction/references/learned-references.md)).
- Scales complexity to purpose with a non-generic floor, then hands a **direction brief** to `design-techniques`.

### 2. [`design-techniques`](skills/design-techniques/) — supplies *how to execute it*
The **technique-vocabulary** layer. It names the distinctive frontend techniques a layperson doesn't know to ask for and carries each one's award-tier-vs-generic implementation params.

- A deep **technique menu**: scroll-scrub, kinetic type, magnetic elements, custom cursor, view transitions, mesh/shader gradients, clip-wipe, scrollytelling, grain, and more — each with the params that separate real craft from the median default ([`references/technique-menu.md`](skills/design-techniques/references/technique-menu.md)).
- **Self-routes by buildability** (plain-CSS / needs-library / builder-assisted) so it fits a simple build, not only an expert one.
- Builder-agnostic — works alongside `frontend-design`, GSAP, and Motion, or drives a direct build.

---

## How they work together

```
context-engineering  (brings the brief, taste, research)
        │
        ▼
design-direction   →  decides the aesthetic direction  →  DIRECTION BRIEF
        │
        ▼
design-techniques  →  builds it to award-tier params
```

`design-direction` chooses the concept; `design-techniques` executes it. Used as a pair, they aim to **replace and exceed** a generic frontend build.

---

## Installation

Clone into your Claude Code skills directory:

```bash
git clone https://github.com/Shadmet-Azat/DesignDirectory.git
cp -r DesignDirectory/skills/design-direction  ~/.claude/skills/
cp -r DesignDirectory/skills/design-techniques ~/.claude/skills/
```

Then invoke them from Claude Code when a build is aesthetic but under-specified — e.g. *"make it beautiful,"* *"design a landing page,"* *"add some animation."*

---

## Repository structure

```
skills/
├── design-direction/
│   ├── SKILL.md
│   └── references/
│       ├── movements.md
│       ├── ai-asset-generation.md
│       └── learned-references.md
└── design-techniques/
    ├── SKILL.md
    └── references/
        ├── technique-menu.md
        └── community-data.md
```

---

Built by [Shadmet Azat](https://github.com/Shadmet-Azat).
