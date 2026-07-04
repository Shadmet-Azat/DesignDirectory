# AI asset generation — art-direct it, emit the prompts

A beautiful page needs bespoke assets; their absence is half of why generic AI sites look generic. You **art-direct
the assets and emit the generation prompts + a layer plan** — you do not generate pixels (the human runs the image
model and drops the assets in, then design-techniques composes them).

## Pick the model per asset type (current — verify; this space moves fast)
- **GPT Image 2.0** (OpenAI) — precision, text-in-image, complex multi-element scenes, image editing, and
  **character/style consistency from a reference image**. Use for **hero / branding / a coherent visual world**.
  Premium.
- **Nano Banana 2 / Pro** (Google, Gemini 3 Pro) — **fast + cheap at volume**, photoreal, multi-language text,
  branding consistency, quick edits. Use for **generating many on-brand assets** and variations.
> Recommend the model in the prompt block; if the human only has one, adapt the prompts to its strengths.

## The craft rule that prevents slop: consistency across the SET
A pile of mismatched generations looks **worse** than none. Generate the whole set in **one visual world** — same
style, palette, light, era — using the models' reference-image / branding-consistency features. This is the asset
analog of "one defended idea."

## Prompt web-ready (not just "a nice picture")
- **A cleanable background for anything to be composited/layered** — so cutting it out is trivial, not a keying
  nightmare. In order of preference: **(1)** a **true transparent PNG** — say it explicitly: *"transparent
  background, export as PNG with alpha (not JPEG)."* A flattened JPEG bakes the transparency **checkerboard in as
  real pixels** — the exact trap that wrecks keying. **(2)** If the model can't output alpha, demand a **flat,
  uniform, single solid background colour that does NOT appear in the subject** — a chroma key (e.g. pure green
  `#00FF00` for a warm coffee/citrus subject): *"solid flat #00FF00 background, uniform, no gradient, no shadow, no
  scene, no checkerboard."* One far-from-subject colour keys out in a single line; a checkerboard or a scene does not.
  Never accept a gradient/scene background for something you intend to cut out. **(3)** Prefer baking small elements
  INTO the main opaque image when possible (one fewer cut-out = one fewer keying risk).
- **Locked light direction + perspective across the set** — mandatory if the assets will be parallax layers, or
  the depth breaks.
- Correct aspect ratio; deliberate **negative space where text will overlay**.
- State the movement/world explicitly (from `movements.md`) so assets match the direction.

## Images > video on cost
Generated **video is the expensive medium.** Get video-like richness from **cheap images + layering/motion**
instead (below). Reserve generated video for the rare moment that truly needs it, and name the cost in the brief.

## The "interactive layers" look — hand design-techniques one of two plans
1. **Manual separated layers** — generate **foreground / midground / background as separate transparent PNGs**
   (one world, shared light), then design-techniques parallaxes them on scroll/mouse. Cheaper, full control.
2. **Depth-map 2.5D** — generate ONE image → depth estimation (MiDaS / DepthFlow) → displacement shader
   (Three.js / Pixi) → mouse/scroll/tilt fake-3D. More immersive; needs WebGL. Note it for the builder-assisted tier.
> Craft rule for both: shared light + perspective, clean edges, high-res source, perf + reduced-motion honored.

## What to put in the DIRECTION BRIEF's `assets` field
For each asset: its role · the world/style line · the recommended model · the **ready-to-run prompt** · and
whether it's a flat asset or part of a layer plan (and which layer). Keep the set coherent; fewer, better assets.