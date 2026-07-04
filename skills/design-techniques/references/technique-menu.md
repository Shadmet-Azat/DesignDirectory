# The technique menu

Up to five fields per entry: **what it is · when it fits · when it's slop · how it's built · award-tier vs generic.**
Read the slop field every time — it's what keeps the menu from becoming a louder rut.
The 5th field (**award-tier vs generic**) carries the *keywords-inside* — the real parameters and the
qualitative gap that separate a crafted implementation from a recognisable one. It's being added entry by
entry as each technique is studied against real source; entries without it haven't been deepened yet
(don't read its absence as "no gap"). Params in deepened entries are marked from real implementations;
**[inferred]** flags a synthesised value not read from source — treat those as a starting point, not gospel.
Pick the 1–2 that fit the brief's *intent*, not the most impressive. Grouped by family.

> Fatigue note (corrected against data — see `community-data.md`): the things people *actually* flag as
> "AI" are the **shadcn/Tailwind default kit, AI-purple, gradient hero text, unprompted neon glow, pills,
> emoji-as-icons** — not the Twitter-meme list. **Bento, glassmorphism, and aurora/mesh backgrounds rank at
> the bottom or were rejected as keyword artifacts** — they're over-*cited*, not over-*flagged*. Treat them
> as ordinary techniques with a precondition, not as tells. "Every section fades up on scroll" and
> scroll-jacked storytelling with no story remain genuine fatigue points.

---

## Buildability index — route by who's building (load-bearing for SKILL.md step 2)
Three tiers. For a layperson / no-expert build, restrict to the first two; gate `builder-assisted-only` behind an
explicit "this needs a developer" — those fail ugly without a hand-built fallback. **"Rising on the platform" ≠
"fittest for a non-designer to ship"** (the WebGPU lesson).

- **`easy-plain-CSS`** (native CSS / one inline SVG, no JS library, degrades gracefully): pinned/sticky · snap-scroll ·
  reveal-on-scroll · variable-font animation · marquee/ticker · glassmorphism · grain/noise · blend-modes/duotone ·
  layered-depth/shadow · bento grid · broken/asymmetric grid · carousels (native scroll-snap) · View Transitions
  (same-doc) · stateful hover · skeleton/optimistic.
- **`needs-library`** (GSAP/Lenis/Motion or a vanilla-JS recipe; shippable by a non-specialist following a recipe):
  parallax · scroll-scrubbing · horizontal-scroll · scroll-linked zoom · custom cursor · magnetic · directional media
  swap · text-reveal/split-text · scramble/counter · mesh/shader gradient (live; *static export = easy-plain-CSS*) ·
  scrollytelling · particles (canvas) · page-load/intro · clip-path/mask wipes (animated; *static shape = easy*) ·
  drag/gesture · animated icons · layered-asset parallax / depth-map 2.5D · CSS 3D transform choreography (perspective + matrix3d; *static arrangement = easy-plain-CSS*) · interactive-vector state machines (Rive) *(runtime is a drop-in `<canvas>` recipe; the artboard itself needs the Rive editor + a designer)* · virtualized scroll journey (proxy scroll → normalized progress; *engine-wired = builder-assisted*) · scroll-reactive nav indicator.
- **`builder-assisted-only`** (needs a specialist + a hand-built fallback; fails ugly without one): WebGL hover
  distortion · Liquid Glass · refractive/dispersion glass material · **subsurface-scattering / lit-from-within material** · refractive media cards (hover-resolve → click-to-enter) · interactive instanced-scale surface · spatial 3D/MSDF type + chromatic-split · interactive 3D viewer · **3-D scene POI waypoints (projection + dolly-to-focus; the HUD `<button>`/DOM-mirror layer is needs-library)** · **volumetric atmosphere (god-rays/fog/particulate — cheap sprite+fog tier is needs-library; a true scattering post pass is builder-assisted)** · shader post-processing · GPU-compute particle fields (WebGPU/TSL) · spatial walkthrough / gaussian splatting · **WebGL app shell (one-canvas engine — top-0.1%, the ceiling to aim past on weight, never a layperson's floor)**.

---

## 1 · Scroll-driven

**Parallax** — background and foreground layers move at different speeds to fake depth.
- *Fits:* hero sections, editorial storytelling, anything wanting tactile depth on a flat page.
- *Slop:* applied to every section; heavy multi-layer parallax that lags scroll; on a content/utility site where it just slows reading. The most overused scroll effect — use sparingly.
- *Built:* CSS `scroll-timeline` (no JS) for simple cases; GSAP ScrollTrigger for control; transform translateY, never top/margin.

**Scroll-scrubbing (scrubbed animation)** — an animation's playhead is tied to scroll position; scrolling scrubs it forward/back. Includes **frame-by-frame image-sequence scrub** (Apple AirPods-style product spin) and scrubbed video.
- *Fits:* product reveals, "show how it's made," a single hero moment that rewards the scroll. The premium move when there's a real object/process to show.
- *Slop:* scrubbing a decorative animation with no payoff; hijacking scroll speed so the user fights the page; huge image sequences that blow the data budget.
- *Built:* GSAP ScrollTrigger. **`scrub` choice is load-bearing:** use `scrub: true` for an image-sequence (frame must map *exactly* to progress: `frame = Math.round(self.progress * totalFrames)`); use a numeric `scrub: 0.5`–`1` for *property tweens* where the ~0.5–1s catch-up lag is the buttery feel — never put numeric smoothing on a frame map, it desyncs.
- *Award-tier vs generic:* **render frames to `<canvas>`, not `<img>` swaps** — `<img>` flickers and desyncs during rapid changes; canvas renders in-memory with no DOM reflow. Real counts run **~900–1200 frames** for a hero spin (a real case: 1182 desktop / 880 mobile, device-specific sets per aspect ratio), **not ~60**. Pipeline: FFmpeg `fps=30` → WebP at quality ~80. **Frame sequence beats a scrubbed `<video>`** (video `currentTime` stutters on mobile, hits autoplay limits, loses fidelity). Staged load: first ~10 frames instant → show frame 1 → background-load the rest via a parallel queue; preload **5 frames in the scroll direction** each tick. Generic = a `<video>` scrub or `<img src>` swap over ~60 frames; award = canvas-rendered WebP sequence, ~1000 frames, staged + directional preload, `scrub:true`. *(Source: Codrops/Zajno OPTIKKA case study.)*

**Video-scene sequencing (autoplay-on-scroll cinema)** — the page moves through a series of pre-rendered, muted, looping product/scene *videos* (not real-time 3D, not a scrubbed frame sequence): each scene is its own short clip that plays when scrolled into view and pauses when it leaves. The cheap, art-directable way to get film-grade product cinema without a WebGL pipeline.
- *Fits:* product/brand stories with a library of bespoke renders; showing one physical object across many contexts (markets, applications, colorways); when the "wow" is photoreal material, not interactivity.
- *Slop:* one giant autoplaying video with no poster (blank first paint, huge download); clips that keep playing off-screen draining battery/CPU; a decorative clip with no payoff; using video where a real-time 3D configurator would let the user explore and convert better; no reduced-motion path.
- *Built:* per scene a `<video muted loop playsinline preload="auto" poster="scene.jpg">` shown poster-first, `.play()`-ed by an IntersectionObserver only while in view (the `autoplay-onscroll` pattern) and `.pause()`-ed when it leaves; sequence/pin the scenes with GSAP ScrollTrigger over a Lenis smooth-scroll base; a signature intro can assemble the world from a sticky-pinned grid of clip-path `inset()`-revealed video tiles. Drive a NAMED easing vocabulary as CSS vars, not `ease` (Penner curves: `expo.out cubic-bezier(0.16,1,0.3,1)`, `expo.inOut cubic-bezier(0.87,0,0.13,1)`, quad `(0.45,0,0.55,1)`, + one slight-overshoot `cubic-bezier(0.05,0.76,0.38,1.015)` for the "alive" pop).
- *Award-tier vs generic:* generic = a single hero `<video autoplay loop>` with no poster and no scroll relationship (blank flash, plays forever off-screen, one clip). Award = **a LIBRARY of consistent bespoke scenes** (one light/perspective world across dozens of renders), each **poster-backed** so first paint is never blank, **autoplay gated to in-view** (IntersectionObserver play/pause = the perf + battery signal), sequenced/pinned to scroll, motion governed by a named easing system, and — the real separator — the whole thing offered ALSO as an efficient non-linear view of the same content (see design-direction's dual-view principle) so length never traps the utilitarian visitor. *(Source: toptier.relats.com — WordPress theme, Lenis + GSAP ScrollTrigger, 2026.)* `[inferred]`: exact scene counts/durations (hero observed 1280×720, ~10s loop).
- *Buildability:* **needs-library** (GSAP ScrollTrigger + Lenis; the video + IntersectionObserver layer is a vanilla-JS recipe).

**Pinned / sticky sections** — an element sticks in the viewport while content scrolls past or animates within it.
- *Fits:* step-by-step walkthroughs, before/after, a stat that counts while pinned, horizontal-scroll triggers.
- *Slop:* pinning so much that the page feels stuck; pinning on mobile where viewport height is precious.
- *Built:* CSS `position: sticky` for simple pins; GSAP ScrollTrigger `pin: true` for animated pins.

**Horizontal scroll section** — vertical scroll input drives a horizontal track (galleries, timelines, "chapters").
- *Fits:* portfolios, product line-ups, timelines, a deliberate genre break in a vertical page.
- *Slop:* a whole site that scrolls sideways (disorients, breaks find-in-page and a11y); no scrollbar affordance so users don't know it's there.
- *Built:* GSAP ScrollTrigger pinning a wrapper + translating a track on scroll progress; give a visual progress cue.

**Scroll-linked zoom (zoom in / zoom out on scroll)** — content scales up into focus or pulls back to reveal a larger scene/grid as you scroll.
- *Fits:* a hero that zooms out into the layout; a map/scene reveal; one cinematic transition between two states.
- *Slop:* repeated zoom on every section (nausea); a single flat `scale()` on one element (cheap CSS zoom, no depth); zoom that fights reduced-motion users.
- *Built:* GSAP ScrollTrigger, numeric **`scrub: 1`** for the ~1s buttery catch-up (NOT `scrub:true` — this is a property tween, not a frame map), `pin: true`, `scale` + `transform-origin` (the focal point). Layered version: pair **ScrollSmoother** (`smooth: 1.5`, `effects: true`) and drive per-layer depth via `data-speed`/`data-lag`; reveal through a soft-edged mask image not a hard rectangle; **`normalizeScroll: true`** for mobile/perf; `ease: power1.inOut`. Honor `prefers-reduced-motion` (cut to end state).
- *Award-tier vs generic:* **generic = one element, one `scale: 1 -> ~2`, `transform-origin: center`, linear scrub, no pin** — the page just enlarges a picture (the parallax of zooms). **Award = depth through *differential* motion:** the subject scales while a background grid/scene scales at a *different* rate (per-layer `data-speed`/`data-lag`), the section is **pinned** so the zoom owns the viewport for its beat, the reveal rides a **soft mask** so the new scene bleeds in, and **`scrub: 1`** gives the cinematic lag. Reference = the Telescope layered zoom (ScrollSmoother `smooth: 1.5` + `effects: true`, `power1.inOut`, `normalizeScroll`). *(Source: Codrops 2025 "Building a Layered Zoom Scroll Effect with GSAP ScrollSmoother and ScrollTrigger.")* `[inferred]`: exact scale stops (~`1 -> 1.6-2.5` focal, background a fraction) — treat the *ratio between layers* as the principle, not a fixed number.
- *Buildability:* **needs-library** (GSAP ScrollTrigger; the layered tier also needs ScrollSmoother).

**Snap scroll** — scroll locks to section boundaries, one "slide" at a time.
- *Fits:* full-screen section decks, onboarding, image stories, mobile card flows.
- *Slop:* on long-form content where it traps the user mid-read; mandatory snap that overrides intent.
- *Built:* CSS `scroll-snap-type: y mandatory` + `scroll-snap-align` — native, cheap, accessible. Prefer `proximity` over `mandatory` for content pages.

**Reveal on scroll (enter animations)** — elements fade/slide/scale in as they enter the viewport.
- *Fits:* giving rhythm to a long page; directing attention to one element.
- *Slop:* THE default tell — every block fading up the same way = generic. Big offender. Vary it, stagger meaningfully, or skip it. Never animate content the user is waiting to read.
- *Built:* Intersection Observer + CSS transition; GSAP `batch`; CSS `animation-timeline: view()`.

**Virtualized scroll journey (proxy scroll → normalized progress → scenes)** — the page has no real document scroll; a hidden **tall proxy element** captures scroll input and its position is mapped to a **single normalized progress value** that drives a sequence of scenes/router-states in a canvas or pinned stage. How one-`<canvas>` experiences (and long scrollytelling rigs) get a multi-scene "journey" out of a surface that itself doesn't scroll.
- *Fits:* full-canvas WebGL sites (see *WebGL app shell*), long scene-to-scene narratives, any experience where scroll should drive an *engine* rather than move DOM.
- *Slop:* hijacking scroll so the native scrollbar / `Ctrl-F` / keyboard / assistive-tech break with nothing given back; a giant virtual height with no wayfinding or progress cue; no reduced-motion path (a pure scrub with no static state); reinventing this for a page a normal document scroll would serve.
- *Built:* a scrollable proxy `<div>` (overflow + a tall inner spacer) whose `scrollTop` → a normalized `progress` (`scrollTop / (scrollHeight - clientHeight)`), read each rAF and eased; `progress` drives camera/scene state and flips **router states** at thresholds (deep-linkable URLs per scene). Keep a visible progress affordance; mirror major scenes as real anchors for a11y; honor reduced-motion with jump-to-scene. *(Exactly how activetheory.net drives its ~47.6k-px home→work→footer journey — a `.FXScroll` proxy → a normalized `ViewController/scrollV` → router scenes, verified live by driving that element directly.)*
- *Buildability:* **needs-library → builder-assisted** (the proxy→progress recipe is vanilla JS; wiring it to a WebGL engine + an a11y mirror is builder-assisted).

---

## 2 · Cursor & pointer

**Custom cursor** — replace/augment the native pointer (dot + ring follower with lerp, context-aware label, blob, trail).
- *Fits:* portfolios, agencies, immersive/experiential sites; a cursor that says "drag", "view", "play" over interactive zones.
- *Slop:* hiding the real cursor on a content/commerce site (hurts usability); laggy follower; pure decoration with no informational role. Desktop-only — never the load-bearing interaction.
- *Built:* **two layers** — a tiny ~5px dot tracking raw `clientX/clientY` *instantly* each rAF tick (no smoothing) **+** a larger ring that **lerps**: `last = lerp(last, target, 0.2)` (~0.15–0.2 is the sweet spot; lower = dreamier, higher = snappier). `cursor: none` on page + interactive els. Start off-screen at `(-100,-100)` so it doesn't flash top-left on load. Change state on hover of `[data-cursor]` zones; fall back to native on touch.
- *Award-tier vs generic:* generic = one ring chasing the pointer, `cursor:none`, no states, no touch/keyboard fallback → laggy and actively breaks usability. Award = **instant dot + lerped ring** (the dual-layer split is the craft signal), **state-aware**: on hover it lerps the ring to the element's *center* (`left+width/2`) and scales it (×1.08/frame to a cap, ×0.92 back) so it "sticks" to buttons — and it keeps the native cursor + `:focus-visible` intact for non-mouse users. Organic-blob variant = 8-segment polygon + SimplexNoise (`noiseScale 150` = speed, `noiseRange 4` = distortion), `.smooth()` per frame. Desktop enhancement only, never load-bearing. *(Source: Codrops "Custom Cursor Effects".)*

**Magnetic elements (magnetic buttons)** — buttons/links pull toward the cursor as it nears, snapping back on exit.
- *Fits:* a single hero CTA, nav items on a crafted site — adds "this is alive" without much cost.
- *Slop:* every button magnetic (chaos); strong pull that makes targets hard to click; on dense UIs.
- *Built:* on pointermove, translate by the offset from the element's *center* (`cursorX − (rect.left + rect.width/2)`); spring back to `{x:0,y:0}` on `mouseleave`; disable on touch. GSAP: `gsap.quickTo(el, "x", {duration: 1, ease: "elastic.out(1, 0.3)"})` (+ a `"y"`) — `quickTo` not `gsap.to`-per-move, for per-frame perf. Framer Motion: `transition={{type:"spring", stiffness:150, damping:15, mass:0.1}}` (low mass = snappy).
- *Award-tier vs generic:* the separator is the **pull strength + trigger zone**, not the existence of the effect. Generic pulls **1:1** (button teleports to the cursor inside its own box, fires only once you're basically on it) with a linear ease. Award pulls a **fraction ~0.2–0.4** of the delta [inferred], from an **expanded/padded trigger zone larger than the visible button**, with an **elastic/spring snap-back**, and often a **secondary inner parallax** — the label moves *further* than the shell (~0.6 vs ~0.3) for depth. Round-1's magnetic CTA "added nothing" because it was the generic 1:1 version. *(Source: olivierlarose.com magnetic-button; Cuberto pattern.)*

**Directional / cursor-driven media swap** — content responds to pointer direction or position; e.g. two videos where moving right plays the "looking right" clip and left the other, or an image that distorts/follows the cursor.
- *Fits:* a signature hero moment, a playful product, a single "wow" interaction.
- *Slop:* gimmick with no meaning; heavy WebGL for a trick most users won't discover; no touch equivalent.
- *Built:* pointer position → drive video `currentTime`, swap sources, or feed a WebGL displacement shader (Three.js / OGL).

**Hover image distortion / WebGL hover** — images ripple, displace, or RGB-split on hover.
- *Fits:* photography, fashion, agency galleries where the imagery is the product.
- *Slop:* distortion that obscures the image; on e-commerce where users want to *see* the product clearly; everywhere at once.
- *Built:* Three.js/OGL plane + displacement map driven by hover progress; static image fallback.
- *Award-tier vs generic:* generic = a whole-image ripple / RGB-split on hover — a decorative wobble that encodes nothing. Award = a **pointer-driven REVEAL between two registered layers**: the cursor paints away a top layer to expose a *meaningful* second layer beneath, along a **soft liquid-diffusion mask** (organic edges that trace the pointer, not a hard shape), often over a constant depth/wireframe armature — and the reveal **means something** (you uncover a second truth about the subject with your own hand), not distortion for its own sake. Reference = the **landonorris.com hero** (verified live): moving the cursor paints the driver's *real helmet livery* over his bare face (the machine-self under the human-self), soft-masked, over a ghosted 3D-helmet wireframe — built on a multi-pass image set (`diffuse/depth/normal/alpha/shadow`, see *Layered-asset parallax & depth-map 2.5D*) with the reveal mask driven by pointer position. Always ship a static fallback + reduced-motion. *(Source: landonorris.com by OFF+BRAND; see design-direction learned-references → Lando Norris.)*
- *Buildability:* **builder-assisted-only** (WebGL shader + a hand-built static/reduced-motion fallback).

---

## 3 · Kinetic typography (type in motion)

**Text reveal (split-text)** — headlines animate in by character, word, or line (stagger, mask-up, blur-in).
- *Fits:* hero headlines, section intros — the highest-ROI "crafted" signal for the least risk.
- *Slop:* animating body copy the user is trying to read; over-long staggers that delay comprehension; the same effect on every heading.
- *Built:* GSAP SplitText (now free) or CSS; animate `transform`/`clip-path`, not `opacity` alone; respect reduced-motion.

**Variable-font / weight animation** — animate font weight, width, or a custom axis on hover/scroll.
- *Fits:* expressive editorial, type-led brands, interactive logos.
- *Slop:* illegible mid-animation states; gratuitous wobble; on UI text that needs to stay readable.
- *Built:* CSS transition on `font-variation-settings`; pair with a variable font.

**Marquee / infinite ticker** — a continuously scrolling strip of text or logos.
- *Fits:* logo walls, "now playing", brand statements, a retro/editorial accent.
- *Slop:* the lazy way to fill space; fast marquees that can't be read; as a substitute for real hierarchy. Heavily overused.
- *Built:* duplicate the track and translate on loop (GSAP or CSS); pause on hover; slow enough to read.

**Scramble / decode / counter** — text scrambles then resolves; numbers count up.
- *Fits:* stats, loading states, techy/terminal aesthetics, a single attention beat.
- *Slop:* on every number; counters that delay real information; scramble that flickers distractingly.
- *Built:* GSAP ScrambleText / a counter tween; `font-variant-numeric: tabular-nums` so digits don't jump.

**Spatial 3D type (extruded / MSDF) with chromatic-split transition** — headlines that live in the *3D scene* (extruded, or crisp **MSDF** glyphs on a plane), moving with the camera/scroll in depth, and hitting a **chromatic RGB-split** distortion as they enter/leave. The type is part of the world, not an overlay.
- *Fits:* WebGL/experiential sites where titles should share space and lighting with the 3D (a project title on a card, a section head that flies through the scene); a single signature transition beat.
- *Slop:* RGB-split on every heading (a decorative glitch that means nothing — the aberration-as-sticker tell); illegible mid-transition states; 3D type where flat DOM type is sharper and cheaper (most of the time).
- *Built:* **MSDF text** (`troika-three-text` / a GLText layer) renders resolution-independent glyphs inside the WebGL scene, transformable in 3D and lit with the world; drive position/rotation from scroll progress; the entry/exit **chromatic aberration** = sample the text pass at R/G/B with a small pointer-/velocity-scaled offset in a post pass. Keep the split brief and tied to motion (fast movement → more split, settle → crisp), not a constant wobble; ship a static DOM-text fallback for reduced-motion/a11y.
- *Award-tier vs generic:* generic = flat DOM headings with a permanent CSS RGB-shadow "glitch." Award = **type that belongs to the 3D world** (shares depth + lighting), crisp via MSDF, with a chromatic split that's **velocity-linked and transient** (reads as motion energy, then resolves to legible). Reference = **activetheory.net** project titles — 3D/MSDF type that moves on scroll and RGB-splits on transition (user-confirmed live). *(See design-direction learned-references → Active Theory; MSDF type also appears in the depth-map 2.5D entry.)*
- *Buildability:* **builder-assisted-only** (WebGL text layer + post pass + DOM fallback).

---

## 4 · Surface & material

**Glassmorphism** — frosted translucent panels (background blur + low opacity + thin border).
- *Fits:* overlays, nav bars, cards floating over imagery/gradient — when there's something *behind* worth blurring.
- *Slop:* over a flat solid background (no depth to justify it); stacked until contrast and legibility fail; as the whole aesthetic. Named fatigue point — use as an accent, not a theme.
- *Built:* `backdrop-filter: blur()` + semi-transparent bg + subtle border/inner-shadow; check text contrast over varying backdrops.

**Liquid Glass (Apple, 2025)** — Apple's dynamic material: optical glass that refracts and reflects surroundings *with a sense of fluidity* — it bends light at edges and responds to motion, beyond a static frosted blur.
- *Fits:* Apple-platform-adjacent UIs, premium device/OS storytelling, controls that float over live content.
- *Slop:* a flat `backdrop-blur` div mislabeled "liquid glass"; imitating it without the refraction/specular edge so it's just glassmorphism with a buzzword; overuse on every surface (Apple itself walked back early excess).
- *Built:* on Apple platforms use the native material; on web it's an approximation — edge refraction via SVG/`feDisplacementMap` or a shader, specular highlights, subtle motion response. Expensive; reserve for the hero.

**Refractive / dispersion glass (real transmission material, not a blur)** — a *3D* glass material that genuinely **refracts** what's behind it and splits light into a chromatic rainbow at its edges (dispersion), reflecting an environment map — the "liquid crystal" / dispersive-gem look. Distinct from Glassmorphism and Liquid Glass, which are 2D `backdrop-filter` approximations: this is a lit mesh with a transmission shader, so a logo/card *bends* the scene through it and edges fringe red→violet.
- *Fits:* a hero logo/mark rendered as a jewel, a refractive product, portfolio cards that read as glass lenses (see *Refractive media cards*), any single showpiece object where "expensive optical material" is the wow. Reserve for the hero — it needs a 3D pipeline.
- *Slop:* a flat `backdrop-blur` div mislabeled "dispersion glass" (no real refraction/fringe = it's just glassmorphism); dispersion cranked so everything rainbow-fringes into mush; heavy transmission on many objects tanking the framerate; no static fallback so non-WebGL users get nothing.
- *Built:* Three.js `MeshPhysicalMaterial` with `transmission: 1`, `ior ~1.45`, `thickness` (refraction depth), low `roughness` for clarity, and an **HDR environment map** to refract/reflect (RGBM/`.hdr` IBL). **Chromatic dispersion** = either the native `dispersion` property (three r167+) or a custom shader that samples the refracted env **3× at slightly offset IORs for R/G/B** and recombines — that offset *is* the rainbow rim. A `MeshTransmissionMaterial` (drei) gives `chromaticAberration`/`anisotropicBlur`/`distortion` knobs directly. Ground it in a real environment so there's something worth refracting; a subtle backdrop makes the material invisible.
- *Award-tier vs generic:* generic = a frosted `backdrop-filter` panel with a hard rim (2D, no bent background, no fringe). Award = a **lit transmission mesh** that visibly *refracts and inverts* the scene behind it, a **restrained chromatic-dispersion fringe only at grazing edges** (not the whole surface), **reflecting a real HDR env** so highlights read as physical, and it **carries the narrative** — e.g. the mark is a *lens* the content plays through, not a decorative gem. Reference = **activetheory.net** — the glass "a" logo + project cards (`Home/refraction`, RGBM env, KTX2/Basis pipeline observed live; the reel/scenes refract *through* the logo). *(See design-direction learned-references → Active Theory.)* `[inferred]`: exact `ior`/`thickness`/dispersion offsets — treat *real transmission + edge-only fringe + HDR env* as the principle, tune the numbers to the object.
- *Buildability:* **builder-assisted-only** (Three.js transmission material + HDR env + a hand-built static/frosted fallback).

**Subsurface scattering / translucent "lit-from-within" material** — a solid that is neither clear glass nor opaque: light enters, **scatters *inside* the volume**, and glows back out, so the object appears lit from within (jade, wax, marble, skin, ice, a crystal with a light trapped inside). Distinct from *Refractive / dispersion glass* (transparent — it bends the *background* through it): SSS is a **translucent solid whose own interior glows** — the light is behind/inside and you watch it diffuse through the material's mass.
- *Fits:* a single hero object where "precious, alive, internally-lit" is the wow — a crystal/gem, an organic form (a creature, fruit, wax), a translucent product; dark scenes where the object should be the *only* light. Reserve for the hero (needs a 3-D pipeline + a backlight).
- *Slop:* cranking scatter until the object is a formless glowing blob (no readable surface/facets); SSS on something that should read hard/opaque (wrong material story); a flat radial-gradient "glow" `<div>` mislabeled SSS (no light transport through geometry = it's just a blur); heavy SSS on many objects tanking the framerate; no static fallback.
- *Built:* Three.js — the honest path is `MeshPhysicalMaterial` with `transmission` + high `thickness` + a warm interior read via `attenuationColor`/`attenuationDistance` (light tints as it travels through the mass) + a **backlight / rim light** so the scatter has something to carry; or a dedicated SSS shader (wrap-diffuse + a baked **thickness map** + back-scatter `pow(dot(-lightDir, viewDir), p)`). Keep `roughness` low-to-mid on facets so the surface still reads. Cheap fake for a *static* hero = a pre-rendered image with the glow baked in (design-direction `ai-asset-generation.md`), parallaxed — no runtime SSS.
- *Award-tier vs generic:* generic = a soft radial glow behind/over a shape (a `box-shadow`/blur bloom) — light *near* the object, not *through* it; reads as a lamp, not a material. Award = **real light transport through a translucent solid** — the interior glow is *shaped by the geometry* (facets/edges channel it, thick parts glow dimmer than thin), tinted by travel distance (`attenuationColor`), **motivated by a backlight**, and it **means something** (a gem/creature that is precious and *alive*, the sole light in a dark scene). Reference = **samanastudio.es** — faceted crystal monoliths glowing warm-gold from within and a translucent bioluminescent koi glowing cyan, each the only light source in a near-black deep-sea void (observed live, Session 33). *(See design-direction learned-references → Samana Studio.)* `[inferred]`: exact `thickness`/`attenuation` — treat *light-through-mass + backlight + geometry-shaped glow* as the principle.
- *Buildability:* **builder-assisted-only** (real SSS = transmission/scatter material + backlight in a WebGL scene + a hand-built static fallback); a *baked-glow static image* parallaxed is **needs-library**.

**Mesh / shader gradients** — multi-point animated color fields, smoothly morphing (not a linear two-stop gradient).
- *Fits:* hero backgrounds, dark-mode ambient depth, brand-color washes, abstract/SaaS pages needing life without imagery.
- *Slop:* the over-bright purple-pink "AI startup" gradient (instant tell); animating so fast it distracts; behind text without a contrast scrim.
- *Built:* `shadergradient` / paper-shaders, a WebGL fragment shader, or a static high-res mesh export (cheapest); add a darkening overlay under any text. The flow is **noise functions warping a color field over time** in a `<canvas>` shader — a CSS `linear`/`conic-gradient` *cannot* do it. **Pause off-screen** (a scroll/intersection observer disabling the canvas when out of viewport) is the non-negotiable perf floor.
- *Award-tier vs generic:* the tell isn't "a gradient" — it's **unbalanced purple→pink + animated too fast + text dropped straight on top**. Stripe (the canonical reference) uses a WebGL canvas ("minigl") with **4 balanced color stops** (`#6ec3f4` blue · `#3a3aff` blue · `#ff61ab` pink · `#E63946` red — blue+red anchor the pink so it never reads as the bare AI-purple), a **slow** drift, an optional `skewY ~-12deg` tilt, and pauses off-screen. Text legibility is *solved*, not ignored: stacked blend layers (a `mix-blend-mode: color-burn` copy + a low-opacity overlay) or at minimum a darkening scrim. Cheapest credible path = a **static high-res mesh PNG + grain** [inferred] — reads ~90% as good for zero runtime cost; reserve live WebGL for a true hero. *(Source: Hufnagl's Stripe-gradient reverse-engineering.)*

**Grain / noise overlay** — a fine film-grain texture over the page or sections.
- *Fits:* warms up flat digital surfaces, adds analog/editorial/cinematic feel, breaks gradient banding. Low-cost, high-craft signal.
- *Slop:* so heavy it muddies text; animated grain that wastes GPU; as a sticker rather than integrated tone.
- *Built:* one inline SVG filter — `<feTurbulence type="fractalNoise" baseFrequency="0.65-0.9" numOctaves="2-3" stitchTiles="stitch"/>` — rendered to a `<div>` overlay at low opacity, `mix-blend-mode: overlay` (or `soft-light`), `pointer-events:none`, fixed full-viewport. Cheaper still: a single tiled ~128-256px PNG noise tile. Reach for SVG first (resolution-independent, no asset).
- *Award-tier vs generic:* **the craft is in three numbers and one blend choice, not in "having grain."** `baseFrequency` is the grain *size* — useful band is ~0.02-0.2 for a coarse, visible turbulence texture and ~0.6-0.9 for a fine film grain (Mullany's rule via Codrops). `numOctaves` is *detail* and **plateaus at 5** — past `numOctaves="5"` it is "practically unnoticeable," so 2-3 is the sane cheap default and 5 only for a rough-paper lit texture. **`type="fractalNoise"` (smooth) reads as film grain; the default `type="turbulence"` reads as ripples/liquid** — wrong type is the most common generic tell. Generic = a heavy dark-multiply noise PNG at ~15%+ opacity banding the text and reading as dirt; or animated grain (re-seeding every frame) burning GPU for no perceptual gain. Award = a *fractalNoise* SVG at **~3-8% opacity**, `mix-blend-mode: overlay/soft-light` so it modulates luminance not darkens, integrated tone not sticker, and **static**. For a lit/embossed paper grain pair `feTurbulence` with `feDiffuseLighting surfaceScale="2"` + `feDistantLight azimuth="45" elevation="60"` (drop elevation ~40 for more contrast). *(Source: Codrops "SVG Filter Effects: Creating Texture with feTurbulence," Sara Soueidan / Michael Mullany.)*
- *Buildability:* **easy-plain-CSS** (one inline SVG block; no library, no JS unless animated — and animated is the anti-pattern here).

**Blend modes & duotone** — `mix-blend-mode` for text-over-image interplay, duotone-treated photography.
- *Fits:* editorial, music/culture, bold brand moments; text that interacts with imagery behind it.
- *Slop:* unreadable text from a blend gone wrong; duotone on product photos people need to see truly.
- *Built:* `mix-blend-mode` (difference/overlay/multiply); duotone via CSS filters or SVG.

**Layered depth & soft shadow** — stacked elevation, long soft shadows, subtle 3D layering (the "dimensionality" trend).
- *Fits:* giving flat UI hierarchy and tactility; cards, modals, bento tiles.
- *Slop:* harsh drop shadows everywhere (2010s tell); inconsistent light source; fake depth with no system.
- *Built:* a consistent shadow scale tied to one light direction; layered `box-shadow`; restraint.

---

## 5 · Layout systems

**Bento grid** — a grid of varied-size rounded tiles, each a self-contained module (Apple/feature-page style).
- *Fits:* feature overviews, dashboards, "everything we do" sections, marketing pages with mixed content types.
- *Slop:* now extremely common — a default for any feature section; forcing unrelated content into tiles; equal-weight tiles with no focal hierarchy. Use when content genuinely varies in size/importance.
- *Built:* CSS grid with spanning cells; vary tile size by content importance; one tile should lead.
- *Award-tier vs generic:* generic = an even grid of equal rounded cards, one icon + a line of text each, no focal hierarchy — the default "feature section" that reads as a template. Award = a **single-screen "overview / control-room"**: heterogeneous **typed** tiles (a text hero, a product slider, a video tile with a kinetic spec number, an industries list, a full-bleed photo, a colorway carousel) in an asymmetric masonry whose **heights are tuned so every column bottom-aligns to the viewport — no scroll**, so the whole offering is graspable in one frame. Density that exceeds a tile's footprint is handled **through TIME, not space**: each overflowing tile is its own **internal carousel** (product ranges, specs, colorways cycle in place; ~25 on one page) so nothing is dropped and nothing scrolls. Each tile is a self-contained module with its own CTA; one tile leads. This is the efficient half of a dual-view — it pairs with the cinematic scroll (see design-direction's dual-view principle). *(Source: toptier.relats.com/overview — flex columns of typed `.box` tiles, 2026.)*
- *Buildability:* **easy-plain-CSS** (flex/grid columns + tuned heights); the in-tile carousels are **needs-library** (or a native scroll-snap recipe).

**Broken / asymmetric grid** — deliberately off-grid, overlapping, editorial layouts that defy the centred column.
- *Fits:* portfolios, editorial, fashion, brands wanting to feel un-templated and confident.
- *Slop:* broken for its own sake so it reads as a mistake; overlaps that hurt readability or break responsive; chaos without an underlying rhythm.
- *Built:* CSS grid with intentional column/row spans and negative margins; keep an invisible structural logic.

**Carousels / sliders** — horizontally paged content.
- *Fits:* testimonials, product variants, image galleries with a clear "more this way" affordance.
- *Slop:* auto-advancing carousels (users miss content, hate them); hiding primary content behind slides; hero carousels (low engagement, known anti-pattern). Prefer a grid or scroll if the content matters.
- *Built:* scroll-snap track (native, accessible) or Embla/Swiper; visible controls; never auto-rotate critical info. **Experiential-tier variant:** a **WebGL carousel** (`gl-carousel`) with GPU transitions + drag-inertia between slides — the signature-gallery move on award sites (builder-assisted; keep a native scroll-snap fallback + reduced-motion). *(Ref: landonorris.com collabs carousel.)*

**Sticky scroll narrative (scrollytelling)** — pinned visual on one side, text steps on the other, visual changes per step.
- *Fits:* data stories, how-it-works, complex explanations that benefit from synced visual + text.
- *Slop:* forcing simple content into a heavy scrollytelling rig; janky step transitions; the "scroll-jacked storytelling" fatigue when there's no real story.
- *Built:* ScrollTrigger steps updating a pinned visual; keep steps few and the visual meaningful.

---

## 6 · 3D & WebGL

**CSS 3D transform choreography (perspective + matrix3d, no WebGL)** — genuine 3D depth built from *pure CSS* `perspective` + `matrix3d`/`rotateY`/`translateZ` on plain DOM/`<img>` elements, choreographed by a single scrubbed ScrollTrigger — no canvas, no Three.js. The lightweight FLOOR of this family: real 3D for a card carousel/gallery without the WebGL weight or risk.
- *Fits:* portfolio/agency showcases, a photo or project carousel that should feel spatial, a hero that morphs its layout in depth on scroll — anywhere the "3D" is *arranged planes*, not a lit model.
- *Slop:* reaching for Three.js to spin a few image cards (inverse slop — CSS does it for ~1% of the weight); a flat `rotate`/`scale` with no `perspective` (reads as a 2D tilt, not depth); a morph that means nothing about the content (decoration — the Vanity trap); no reduced-motion end-state (a pure scrub leaves reduced-motion users with nothing).
- *Built:* a tall spacer section holds a **`position: sticky` + `perspective: 1400px` + 100vh stage** (sticky keeps the scene centred while the section scrolls past — cheaper + more accessible than GSAP `pin: true`, no pin-spacer reflow); each card is an absolutely-positioned wrapper around an `<img>`, transformed by `matrix3d` (rotateY + translateZ + translateY); ONE GSAP ScrollTrigger on the section with **`scrub: 0.12`** (light lag = buttery) drives a master timeline start `0` → end = full section scroll. `transform-style: preserve-3d` where nesting needs it; ship a static/reduced-motion end-state.
- *Award-tier vs generic:* generic = one element `rotateY`-spinning on scroll, or a heavy WebGL scene for what CSS can do — depth that's either fake (no perspective) or over-built. Award = **a state-change MORPH between two arrangements** (e.g. a flat 2D tilted RING of cards at rest → an overlapping fanned 3D deck receding into depth), all from CSS `matrix3d` under one shared `perspective`, on a **sticky (not pinned) stage**, with a **light `scrub` (~0.1–0.2)** for the lag, and — the real separator vs the Vanity reference itself — the choreography **means something** (cards *assembling into a system*, not an arbitrary spin) and the cards **click through** so the showpiece converts instead of dead-ending. Change of *arrangement* reads as more designed than a rotation; the zero-WebGL weight is the craft flex. *(Source: vanity.llc — Webflow + GSAP ScrollTrigger, `perspective:1400px` sticky stage, 8 `.vty-mc` cards via `matrix3d`, one `scrub:0.12` trigger; observed live Session 31, 2026-07-03; see design-direction learned-references → Vanity Studio, the CSS/decoration inverse of Lando's WebGL/meaning.)* `[inferred]`: exact master-timeline easing; the card click-through wiring (behaviour user-confirmed, mechanism not driven).
- *Buildability:* **needs-library** (GSAP ScrollTrigger for the scrubbed morph; a *static* 3D arrangement with no scroll is easy-plain-CSS). Degrades to a flat 2D layout cleanly.

**Interactive 3D object / product viewer** — a real-time 3D model the user can spin, explore, configure.
- *Fits:* physical products, hardware, configurators, brand showpieces where the object *is* the story.
- *Slop:* a 3D blob with no purpose; heavy models that stall load; 3D where a sharp photo would convert better.
- *Built:* Three.js / React Three Fiber, or Spline (no-code) for simpler scenes; compress (glTF/Draco); LOD + loading state + 2D fallback.

**Refractive media cards (hover-resolve → click-to-enter)** — portfolio/gallery cards that are **refractive glass lenses at rest** (you can't quite read them), **RESOLVE to a playing video / live graphic on hover** (the glass clears and the embedded media plays), and **click through into a fully cursor-interactive scene**. Browsing itself becomes an act of discovery — the content hides behind the material and rewards the pointer.
- *Fits:* high-craft studio/agency portfolios, an experiential case-study index, a showcase where each item is itself a rich media/interactive piece — anywhere you want the *card* to demonstrate craft, not just thumbnail it.
- *Slop:* refraction so heavy the card is unreadable even on hover (obfuscation, no payoff); a "hover video" that's really just an autoplaying muted loop with no glass/resolve beat; a card that dead-ends instead of entering an actual scene; every card identical (the resolve should reveal *this* project — the best versions make each card bespoke); desktop-only with no tap/focus path.
- *Built:* each card = a plane with a **transmission/refraction material** (see *Refractive / dispersion glass*) over a **video texture** (`VideoTexture`) or a live render target; on `pointerenter` tween the material's transmission/roughness/dispersion **down** (glass → clear) and `video.play()`; on `pointerleave` reverse + pause; on click, route to a detail scene (`View Transitions` / a router state) where the same media becomes interactive. Lazy-load each card's video; play **only the hovered** card (never all at once); deep-link each (`/work/<slug>`) so detail views are shareable; ship a static poster + a real focusable link as the fallback.
- *Award-tier vs generic:* generic = a grid of cards that swap to an autoplay `<video>` on hover — flat, all playing, no material, dead-ends on click. Award = **glass-at-rest that optically RESOLVES to live media under the pointer**, each card **unique**, the click **enters an interactive scene** (not a static case page), built as real GPU cards inside the same canvas as the rest of the world. The three beats — *hide (glass) → resolve (hover video) → enter (click-interactive)* — are the craft; the material makes the reveal feel earned. Reference = **activetheory.net** WORK: cards branching off the 3D spine, each a refractive lens that resolves to embedded video on hover and clicks into a per-project interactive scene (`WorkItemShader`, deep-linked `/work/echo`; user-confirmed live). *(See design-direction learned-references → Active Theory.)* `[inferred]`: exact hover tween curves/durations.
- *Buildability:* **builder-assisted-only** (transmission material + video textures in a WebGL scene + a static/focusable fallback).

**Interactive instanced-scale surface (fish-scale / chainmail / sequin)** — a large surface tessellated from **thousands of small instanced tiles** (hexagons, scales, sequins) that individually react to the pointer — tilting, flipping, lighting or flushing colour in a wave that trails the cursor. A tactile "living material" panel.
- *Fits:* one signature section on an experiential site (a lab/innovation panel, an interactive banner, a texture that invites touch), a background that rewards the pointer without stealing focus.
- *Slop:* the whole page paved in reactive scales (GPU drain + no focal point); a sequin/scale effect that means nothing and just wobbles; per-tile DOM elements (thousands of nodes → jank; must be instanced); no touch/reduced-motion path.
- *Built:* **GPU instancing** (Three.js `InstancedMesh` / instanced attributes) so N-thousand tiles are one draw call; per instance the shader reads pointer distance → drives rotation/scale/emissive/colour with a smoothed falloff (a soft radius trailing the cursor); baked lighting or a matcap keeps it cheap. Cap the interaction radius; pause off-screen; degrade to a static textured panel on touch/reduced-motion.
- *Award-tier vs generic:* generic = a CSS grid of divs flipping on hover (heavy, low count, no falloff) or a flat decorative texture. Award = **thousands of instanced tiles reacting as one field** with a smooth pointer-trailing wave, physical lighting/iridescence per tile, one draw call — a surface that reads as a real material you're disturbing. Reference = **activetheory.net "THE LAB"** — an interactive hexagonal-scale surface under an underwater caustic (`hexgrid`/`chainlink` geometry; user-confirmed interactive). *(See design-direction learned-references → Active Theory.)* `[inferred]`: exact falloff radius + per-tile response.
- *Buildability:* **builder-assisted-only** (instanced WebGL + shader + fallback).

**WebGL app shell (one-canvas engine: GLUI + MSDF GLText + GL-native a11y)** — not one effect but an *architecture*: render the **entire site inside a single `<canvas>`** — text, UI, cursor, transitions, every scene — as a real-time GPU application, with the DOM reduced to a mount node. The maximal end of experiential web (Hydra/Lusion-class). Included here so the menu is honest that this ceiling exists **and** why it's almost never the right answer.
- *Fits:* a real-time studio's own showpiece, a game, a spatial brand "world" — **only when the craft itself is the product** so the weight is earned (see design-direction movement #12 ceiling + the medium=message test).
- *Slop:* going full-canvas for a content/marketing site a few DOM sections would serve better and lighter; **dropping the web's contract** — no crawlable text, no screen-reader tree, no keyboard path, no reduced-motion/low-power fallback (an inaccessible flex); a 30-second load with no low-power path.
- *Built:* a WebGL UI toolkit renders "DOM-like" elements to the canvas (Active Theory's `GLUI`), text via **MSDF font atlases** (`GLText` — crisp at any scale, 3D-transformable), a proxy scrollable element feeds a normalized scroll value (see *Virtualized scroll journey*), and — non-negotiable — **GL-native SEO + a11y shims** rebuild a real crawlable/screen-reader tree that mirrors the canvas UI (`GLSEO`/`GLA11y`). Multi-thread the asset/decode work (worker pool); DRACO + Basis/KTX2 + RGBM-HDR + baked lighting for the asset budget.
- *Award-tier vs generic:* generic = a WebGL hero bolted onto a normal page, or a full-canvas showreel that *abandons* SEO/a11y/reduced-motion (spectacle with no contract). Award = a coherent **engine** where the whole experience is GPU-rendered AND the web contract is honoured inside the GL layer — plus the discipline to only do this when spectacle is the service. Reference = **activetheory.net (Hydra)**: DOM = `<div id="Stage">` + one `<canvas>`, 0 `<img>`, GLUI/GLText/GLSEO/GLA11y, 8-thread worker pool (observed live). *(See design-direction learned-references → Active Theory — logged with its exceed-target: same wonder at a fraction of the weight, with a real low-power path.)*
- *Buildability:* **builder-assisted-only** — top-0.1%; the ceiling to aim *past on weight*, not a floor to ship from. A layperson wanting "a WebGL site" should be routed to a single WebGL hero (interactive-3D-viewer / refractive glass) over a DOM page, never this.

**Interactive vector state machines (Rive)** — real-time, *stateful* vector animation (artboards + state machines) that reacts to scroll, hover, pointer and data inputs and **recolors via colour inputs** — the lightweight sibling to WebGL 3D for making a whole interface feel alive at KB weight. One runtime can drive logo, UI, icons, an interactive diagram, a hero object, and an animating signature.
- *Fits:* a **living-identity system** (personal brand, mascot, product mark), interactive logos / hamburgers / buttons, an interactive map or diagram, a recolorable hero object, scroll-reactive illustration — anywhere you want real-time aliveness *plus a little agency* without a video or a full 3D pipeline.
- *Slop:* a Rive file used as a *looping decoration* (that's just a heavier Lottie — the whole point is INTERACTIVITY/state); a state machine that only plays a canned sequence on scroll and gives no real agency (on-rails); no static / reduced-motion fallback; forgetting the artboard must be authored in the Rive editor, so it needs a designer in the loop.
- *Built:* author artboard + **state machine** in the Rive editor, exposing **inputs** (boolean / number / **trigger**) and **colour inputs** for theming; integrate the `@rive-app/canvas` (or `webgl2`) runtime, one `<canvas>` per artboard. Bind inputs to events — hover/pointer → boolean/number; **scroll position → a number input** (or wire GSAP ScrollTrigger `start`/`end` → `setInput`); **a per-section theme → a colour input** so marks recolor as the page's `theme` flips light/dark. Lazy-load artboards on view, pause off-screen, ship a static SVG/PNG fallback + honor `prefers-reduced-motion`.
- *Award-tier vs generic:* generic = one Rive file autoplaying a loop (indistinguishable from a Lottie but heavier), or a scroll-triggered canned sequence — no state, no agency. Award = **ONE interactive-vector runtime driving a whole reactive FAMILY** — logo, hamburger, buttons, an interactive circuit/diagram, the animating **signature**, and a **recolorable hero object** — all state-machine-driven, scroll/hover-reactive, and **recoloured by one per-section theme input** so the identity stays coherent as the palette flips. The separator is *state + input wiring* (real reactions to the user and the page), not "a vector that moves." This is the cheap, robust answer to "make it feel alive/real-time" when a Three.js scene is overkill (see design-direction movements #12's vector sibling + its dual-answer to passive video). *(Source: landonorris.com by OFF+BRAND — 8 `.riv` files (page-transition · signature · circuits · phrases · ln4 logo · btn-ui) with `riveColorInput` + `riveScrolltriggerTarget/Start/End` + `riveHover` observed; see design-direction learned-references → Lando Norris.)*
- *Buildability:* **builder-assisted to author** (the artboard + state machine need the Rive editor and a designer) → **needs-library to integrate** (the runtime is a drop-in `<canvas>` recipe). Far lighter and more robust than WebGL 3D — degrades to a static image cleanly.

**Particles / generative backgrounds** — reactive particle fields, flow fields, generative art behind content.
- *Fits:* tech/AI/creative-coding brands, a living ambient backdrop, data-as-art.
- *Slop:* the connected-dots-network cliché (instant generic tech tell); particles that fight text legibility; GPU drain on mobile.
- *Built:* Three.js / OGL / canvas; cap particle count; pause off-screen; reduced-motion + mobile fallback.

**GPU-compute particle fields (WebGPU / TSL)** *(rising — builder-assisted only)* — hundreds-of-thousands-to-millions of particles whose positions/velocities are simulated *on the GPU* via compute shaders each frame (flow fields, morphing point clouds, reactive swarms), far past CPU/instanced-attribute systems.
- *Fits:* a true hero showpiece on a creative-coding / AI / 3D-brand site where the *living simulation* is the story; morph-between-shapes point clouds; audio/pointer-reactive swarms. Only when the content genuinely justifies real-time GPU simulation.
- *Slop:* using it for an ambient background a CPU field or a video would cover for 1% of the cost; the connected-dots cliché on a bigger engine; shipping with no fallback so non-WebGPU users get a blank/broken canvas.
- *Built:* Three.js **TSL** + **`WebGPURenderer`**; particle state in `instancedArray`/storage buffers updated by a `compute()` pass each frame (no CPU round-trip). TSL is now the *recommended* path and auto-emits WebGL when WebGPU is absent. WebGPU ships by default as of late-2025 (caniuse ~83.7%).
- *Award-tier vs generic / why-not-for-laypeople:* **hard to ship plain, fails ugly.** The WebGL fallback "may not work as intended" because **compute shaders are not supported in WebGL** — the headline feature is exactly what the fallback drops, so a non-WebGPU user can get a dead/degraded scene unless a builder hand-writes a separate CPU/WebGL path. A non-designer asking for "cool particles" should be routed to the Particles / generative backgrounds entry above unless an expert owns the fallback. *(Source: Maxime Heckel, "Field Guide to TSL and WebGPU," 2025.)* `[inferred]`: ~tens-of-thousands trivially, hundreds-of-thousands-to-millions with compute (hardware-dependent).
- *Buildability:* **builder-assisted-only.**

**Shader effects (post-processing, transitions)** — fragment shaders for distortion, transitions, fluid, dithering, chromatic aberration.
- *Fits:* a single signature transition or hero effect on an experiential site.
- *Slop:* effects stacked until it's a screensaver; aberration/glitch with no concept; ignoring the perf and accessibility cost.
- *Built:* GLSL via Three.js/OGL; or curated libraries; always a static fallback.

**Spatial walkthrough / virtual tour (+ gaussian splatting)** — navigate a real-time 3D *space*: move room-to-room or scene-to-scene via a camera, first-person or guided. **Gaussian splatting** reconstructs a *photoreal* space from ordinary photos (the 2024–25 leap that makes "walk through a real place" look real, not game-engine).
- *Fits:* architecture, real estate, hardware interiors, museums/exhibitions, car configurators, brand "worlds" — when the **space itself is the story** and exploration is the point.
- *Slop:* a heavy scene with no wayfinding (users get lost); no guided path/affordance; a giant download with no loading-as-experience; using it where a few sharp photos convert better; no non-WebGPU/mobile fallback (a dead/blank canvas).
- *Built:* Three.js / React-Three-Fiber / Babylon.js / PlayCanvas. Camera rig = either `PointerLockControls` first-person **or** a hotspot/waypoint camera that animates between framed views (usually the better UX). Geometry = GLTF + Draco compression, **or** a gaussian-splat renderer (`.splat`/`.ply`). Non-negotiables: LOD, progressive/streamed load, a 2D fallback. *(Studios: Active Theory, Lusion; ref Bruno Simon drive-through.)*
- *Buildability:* **builder-assisted-only** (a specialist owns the camera, perf budget, and fallback).

**Layered-asset parallax & depth-map 2.5D** — build cinematic depth from *flat (often AI-generated) images* instead of real 3D or video: either separated FG/MG/BG layers parallaxed, or one image + a depth map displaced for mouse/scroll/tilt "fake-3D." The cheap way to get video-like motion. (Pairs with design-direction's asset *layer plan*.)
- *Fits:* heroes and scenes wanting tactile depth on a budget; image-led stories; the "interactive layers" look without a 3D pipeline or expensive generated video.
- *Slop:* layers whose **light direction/perspective don't match** (the depth visibly breaks); over-strong parallax (nausea); heavy depth shaders on low-end mobile; ignoring `prefers-reduced-motion`.
- *Built:* **manual layers** = separated transparent PNGs (one world, shared light) translated on scroll/pointer via CSS/GSAP. **depth-map 2.5D** = a depth map (MiDaS / DepthFlow) driving a displacement shader in Three.js/Pixi. Both: high-res source, clean edges, reduced-motion → static. Generate the layers web-ready (see design-direction `ai-asset-generation.md`).
- *Award-tier vs generic:* generic = ONE image + ONE depth map with a uniform mouse-follow displacement — it "breathes," but the relief is mushy and the subject floats (no grounding, soft edges). Award = a **multi-pass rig**: `diffuse` + **`depth`** + **`normal`** (real surface relief a depth map alone can't give) + **`alpha`** (a clean matte) + a **separate `shadow` pass** (so the subject is *grounded*, not floating), composited in a WebGL "engine"; the displacement is **subtle + eased**, driven by pointer *and* scroll; type can live in the same GL space as **MSDF** (crisp at any scale, 3D-transformable). The separator is *the extra passes (normal + shadow) + restraint*, not a stronger single-map push — and the best versions make the displacement/compositing **mean** something (e.g. one identity's skin fragmenting onto another face) rather than a decorative ripple. *(Source: landonorris.com hero — `diffuse/depth/normal/alpha/shadow` webp passes + a `data-engine` WebGL canvas + `Brier-Bold-msdf` GPU type observed.)* `[inferred]`: exact displacement scale — treat *multi-pass + subtle* as the principle.
- *Buildability:* manual layers = **easy-plain-CSS → needs-library**; depth-map 2.5D = **needs-library → builder-assisted** (the full multi-pass normal+shadow rig is builder-assisted).

**3-D scene waypoints / point-of-interest hotspots (billboarded HUD → dolly-to-focus + annotation)** — glowing markers **anchored to points on a 3-D object/scene** and projected to 2-D screen space (HUD dots that track as the camera moves); nearing/hovering highlights one, and **clicking dollies the camera in to that point and opens a text card** about it. The "explore this object" interaction — POIs turn a static 3-D showpiece into something the user navigates, and give a one-page site a second navigation axis crossed with scroll.
- *Fits:* a hero object with parts worth explaining (product features, an anatomy/diagram, a map), a manifesto delivered as *stations* on a scene, any WebGL showpiece that should be *explorable* rather than watched; the in-scene focus axis of design-direction's **two-axis navigation** (scroll = deeper, click = closer).
- *Slop:* markers that don't actually track their 3-D anchor (they drift off the point — the projection is faked with static 2-D pins); a hotspot that just toggles a tooltip with **no camera move** (no sense of *approaching*); POIs that aren't real focusable buttons with `aria` (they are navigation — make them accessible); a scene turned into a pin-cushion of markers; a dolly with **no "you are here" / no return** so decoupling focus from scroll gets the user lost.
- *Built:* keep each POI as a real **`<button>` in a HUD layer** (focusable, `aria-label` = the annotation) and each frame **project its world-space anchor to screen** (`vector3.project(camera)` → `translate(x,y)`), scaling/fading by proximity + active state; on click **tween the camera** to a framed offset from the anchor (GSAP on `camera.position` + `lookAt` / `OrbitControls.target`, an eased ~0.8–1.2s dolly) and reveal the card; provide a close/return that restores the prior camera. Anchors ride the object so markers stay glued as it moves. Ship a **DOM list of the same POIs** (labels + text) as the a11y / no-WebGL fallback.
- *Award-tier vs generic:* generic = 2-D pins absolutely-positioned over a canvas that pop a tooltip on click — they fall out of registration the instant the scene moves, and nothing *approaches*. Award = **markers truly welded to 3-D points** (per-frame projection, tracking through camera moves), **active-by-proximity**, and a **click that flies the camera in** so you feel yourself *closing on* the thing before you read about it — plus real focusable buttons with a DOM mirror. Reference = **samanastudio.es** — manifesto "presences" as HUD halo/ring/dot buttons projected from points on the crystals; clicking dollies the camera up to that point and opens a mono card ("A page informs. An experience moves…"), keyboard-focusable, decoupled from the scroll-depth axis (observed live, Session 33 — surfaced by driving the click, not visible statically). *(See design-direction learned-references → Samana Studio; pairs with *Virtualized scroll journey*.)*
- *Buildability:* **builder-assisted-only** for the 3-D→screen projection + camera tween — but the **HUD `<button>`/aria + DOM-mirror layer is a plain-DOM recipe** a non-specialist can wire once a builder exposes the anchor positions.

**Volumetric atmosphere (god-rays · participating media · depth fog · drifting particulate)** — the *environment itself* is rendered: shafts of light through haze (light scattering / god-rays), a **depth-graded fog** that fades and colour-shifts with distance, and slow drifting **particulate** (dust, marine-snow, embers) — so a dark scene reads as a real *place with air in it*, not a black void with an object floating in it. Atmosphere becomes the medium the subject sits in.
- *Fits:* dark/cinematic WebGL scenes where mood is the point (deep sea, space, fog, a cathedral shaft); grounding a single hero object in a believable volume; a descent/journey where depth should *feel* like distance (fog + colour-temperature drift do the felt-depth work).
- *Slop:* a full volumetric/raymarch pass where a cheap billboarded god-ray sprite + a fog colour would sell it for 1% of the cost (GPU drain); particulate so dense it's snow-globe noise fighting the subject; fog so heavy the object vanishes; particles re-seeding every frame for no perceptual gain; no reduced-motion / low-power path (kill drift + the heavy passes).
- *Built:* cheapest credible = scene `fog`/`fogExp2` (distance fade + colour) + a couple of **additive god-ray sprites/planes** angled from a key light + a GPU **points** system for slow particulate (small count, additive, eased drift, depth-faded). Heavier = a **volumetric light-scattering post pass** (radial blur from the light's screen position — the classic god-rays post-process) and/or raymarched fog. Drive a **colour-temperature drift by depth/scroll** (e.g. cool → teal as you descend) for felt distance; add DOF so focus falls off with range. Pause/disable off-screen; cap particle count; reduced-motion → freeze drift + drop the post pass.
- *Award-tier vs generic:* generic = a flat dark background + a static radial-gradient "glow," or a dense particle field with no depth — flat and noisy. Award = **a scene with real air**: light shafts scattering through haze from a *motivated* source, **depth-graded fog that colour-shifts with distance** (so descending *feels* like going deeper), sparse depth-faded drifting particulate, and DOF — atmosphere that carries the mood and the sense of space, and is **motivated** (a deep-sea dive, not decoration). Reference = **samanastudio.es** — god-ray shafts, white marine-snow particulate, a near-black-blue fog drifting to deep teal-green with depth, and DOF, around SSS forms in a deep-sea descent (observed live, Session 33). *(See design-direction learned-references → Samana Studio.)* `[inferred]`: exact fog density / particle counts / post-pass params — treat *motivated shafts + depth-graded colour-fog + sparse drift* as the principle.
- *Buildability:* **needs-library → builder-assisted** (scene `fog` + a few additive god-ray sprites + a small canvas/points particulate layer is a needs-library recipe; a true volumetric-scattering / raymarch post pass is builder-assisted).

> 3D/WebGL is the highest-cost, highest-risk family. Only reach for it when the *content* justifies real-time rendering and the surface can afford the weight. On a utility UI it's almost always inverse slop.

---

## 7 · Transitions & navigation

**View Transitions (page/route morphs)** — elements persist and morph between states/pages instead of a hard cut (shared-element transitions).
- *Fits:* SPA route changes, gallery→detail, list→item — continuity that makes an app feel native and intentional.
- *Slop:* slow transitions that delay navigation; morphing unrelated elements; transition for its own sake.
- *Currency:* **peak-capability (~88.6% browser support), emerging-adoption (shipped real-world usage still lags)** — "you can, and few do it right," not "everyone's doing this." *Buildability: easy-plain-CSS (native, same-doc).*
- *Built:* native View Transitions API (`document.startViewTransition`, `view-transition-name`); Framer Motion `layoutId` / `AnimatePresence` in React; keep them fast (≤300ms).

**Page-load / intro sequence** — a brief orchestrated reveal on first load (logo, mask wipe, staggered entrance).
- *Fits:* portfolios, brand sites, a considered first impression — once.
- *Slop:* a long unskippable loader on a content/utility site; replaying on every navigation; blocking the user from the thing they came for.
- *Built:* a short GSAP timeline; cap ~1s; skippable; never gate critical content behind it.

**Clip-path / mask wipes** — sections or images revealed by an animated shape mask rather than a fade.
- *Fits:* premium reveals, image-to-image transitions, "puzzle"/split reveals, a more crafted alternative to the default fade.
- *Slop:* complex masks that distract; on every element; janky on low-end devices; mismatched point counts that pop instead of morph.
- *Built:* animate `clip-path` (`inset()` / `polygon()` / `circle()`) or an SVG/PNG mask with GSAP `fromTo`. **The load-bearing rule: equal point counts in the start and end `polygon()` — GSAP interpolates point-for-point, and unequal counts snap instead of morph.** Default ease `power2.inOut` for a wipe; `power4`/`expo` for a faster snap. `prefers-reduced-motion` → hard cut/instant state, not a fade.
- *Award-tier vs generic:* **generic = a single `inset()` bar sweeping L→R on a CSS keyframe — one axis, no relationship to the content underneath**; it reads as a slide, not a reveal. **Award = the mask geometry maps to the layout and the wipe is composited with motion in the elements it reveals.** Reference = a cross/puzzle `clip-path` whose arm-width is **computed from the real grid gutter, not hard-coded**: convert the gutter (`5vw`) to a per-axis percentage against the container's measured `getBoundingClientRect()` (`armWidth.x = (armPx/width)*100`, `.y = (armPx/height)*100`), recomputed on resize; the revealed panel is scaled by a matching sub-1 factor per axis so mask edges stay glued to the moving cards; hover debounced ~100ms. Generic ignores this and the seams drift. *(Source: Codrops 2025 "Animated Product Grid Preview with GSAP & Clip-Path," Gwen Bogaert.)* `[inferred]`: point-count parity + `power2.inOut` generalize to any wipe.
- *Buildability:* **needs-library** for animated morphs (GSAP `fromTo` on `clip-path`); a *static* clip-path shape is easy-plain-CSS.

---

## 8 · Micro-interactions & hover

**Stateful hover & feedback** — buttons, links, inputs respond with motion that confirms the action (lift, fill sweep, icon shift, underline grow).
- *Fits:* everywhere — this is the baseline of feeling crafted vs. dead. The cheapest, safest upgrade.
- *Slop:* `transition: all`; effects slower than ~200ms (sluggish); inconsistent across the UI; no focus-visible equivalent for keyboard.
- *Built:* transition specific properties with a real easing curve (e.g. `cubic-bezier(0.23,1,0.32,1)`); exit faster than enter; always pair hover with `:focus-visible`.

**Drag / gesture interactions** — draggable cards, sliders, swipeable decks, pull-to-action with spring physics.
- *Fits:* card decks, comparison sliders, mobile-first flows, playful configurators.
- *Slop:* drag with no spring/inertia (feels broken); desktop-only drag with no click fallback; hidden gestures with no affordance.
- *Built:* Motion drag + `dragSnapToOrigin`/constraints, or GSAP Draggable + InertiaPlugin (Draggable proxy needs a `trigger`).

**Animated icons & state morphs** — icons that morph between states (menu↔close, play↔pause, check draw-on).
- *Fits:* toggles, nav, confirmations — small moments that signal polish.
- *Slop:* morphs slower than the user's intent; animating decorative icons that carry no state.
- *Built:* animate SVG paths / `pathLength`; Lottie/Rive for complex ones; keep snappy.

**Scroll-reactive nav indicator** — a small mark inside the nav (the divider between two labels, a worm/wave/scrubber) that **morphs or slides with scroll position and direction** — a live, characterful progress cue that doubles as decoration.
- *Fits:* one-page / experiential sites with a persistent nav pill, long journeys where a subtle "where am I / which way" signal helps; a place to inject personality without clutter.
- *Slop:* a jerky indicator that fights the scroll; a decorative wiggle that doesn't actually map to position (fake feedback); replacing a real, accessible progress affordance with a cryptic squiggle only.
- *Built:* map scroll `progress` → the mark's transform/morph (slide a marker along the WORK—CONTACT divider, or drive an SVG path's control points / a shader), plus a short direction-reactive skew/stretch from scroll *velocity* (sign + magnitude), eased so it settles. Keep it as *augmentation* of a legible nav, not the only wayfinding.
- *Award-tier vs generic:* generic = a static divider, or a top-of-page progress bar. Award = a **living mark that reads scroll position AND direction** and morphs with personality while still being honest feedback. Reference = **activetheory.net** — the mark between WORK|CONTACT morphs/slides as you scroll up or down (user-confirmed live). *(See design-direction learned-references → Active Theory.)*
- *Buildability:* **needs-library** (a scroll listener + a transform/SVG-morph; a shader version is builder-assisted).

**Skeleton & optimistic loading** — content-shaped placeholders (not spinners) while loading; instant optimistic UI.
- *Fits:* data-driven UIs, anything with a >300ms fetch — makes waits feel shorter and intentional.
- *Slop:* skeletons for sub-300ms loads (flash); spinners where a skeleton fits; fake-long skeletons.
- *Built:* skeleton blocks matching final layout; rule of thumb: skeleton if >300ms, spinner if <300ms.

---

## Tool → technique map (what actually builds these)
- **GSAP** (+ ScrollTrigger, Flip, SplitText, Draggable, InertiaPlugin) — scroll-scrubbing, pinning, horizontal scroll, scrollytelling, text reveal, draggable, scramble. The award-site workhorse.
- **Lenis** (smooth scroll) — the buttery scroll feel under most Awwwards sites; pairs with ScrollTrigger. *Caveat:* fights native scroll/assistive tech — use deliberately, honor reduced-motion.
- **Motion / Framer Motion** — React micro-interactions, gestures, `layoutId` shared-element transitions, `AnimatePresence` exits, spring physics.
- **Native CSS** — `scroll-snap`, `position: sticky`, `scroll-timeline`/`view()` scroll-driven animations, `backdrop-filter`, `clip-path`, `mix-blend-mode`, View Transitions API. Reach here first; it's cheapest and most accessible.
- **Three.js / React Three Fiber / OGL** — 3D objects, particles, shader effects, WebGL hover distortion, refractive/dispersion glass (`MeshPhysicalMaterial` transmission / drei `MeshTransmissionMaterial`), **subsurface-scattering / lit-from-within material** (transmission + `thickness`/`attenuation` + backlight, or a wrap+thickness-map SSS shader), refractive media cards, instanced-scale surfaces, **scene `fog`/`fogExp2` + additive god-ray sprites + a `Points` particulate layer (volumetric atmosphere)**, **`vector3.project(camera)` + a camera dolly for 3-D POI waypoints**. A full one-canvas *engine* (Active Theory's Hydra-class) is the top-0.1% ceiling above it.
- **troika-three-text / MSDF font atlases** — crisp, resolution-independent text rendered INSIDE a WebGL scene (GLText-class), 3D-transformable and lit with the world; how full-canvas sites render type without leaving the GPU.
- **Spline** — no-/low-code 3D scenes and interactions.
- **Rive** — interactive/*stateful* vector state machines (see §6 *Interactive vector state machines*): reactive logos/UI/mascots, recolorable objects, animating signatures — inputs + colour-inputs wired to scroll/hover/theme. **Lottie** — vector *playback* only (no state/inputs). Use Rive when the vector must REACT to the user or page; Lottie when it just plays.
- **shadergradient / paper-shaders** — animated mesh/shader gradients without hand-writing GLSL.

## Current fatigue index (treat as accent, not headline — verify against the live moment)
**Genuinely read as "AI" (the data-ranked real tells):** the bright purple→pink/blue "AI gradient" · gradient hero text · unprompted neon glow · the shadcn/Tailwind untouched default · pill-by-default · emoji-as-icons · "every section fades up on scroll" · scroll-jacked storytelling with no story · auto-rotating hero carousels.
**Over-CITED but NOT actually flagged (Twitter memes — demoted):** bento grids · glassmorphism · aurora/mesh backgrounds · connected-dots particle networks. The data ranks these at the bottom or rejects them — they're fine techniques with a precondition, not tells; don't headline them. Trends move; re-check before treating this list as fixed.

**Watch list (rising / shifting — re-check next pass, Session 25):** GPU-compute particle fields (WebGPU/TSL — rising but builder-assisted, §6) · dithering/halftone shader treatments (rising in the shader space, not yet layperson-shippable — Codrops-led; await ≥2 shipped award winners) · metaballs/fluid-blob (calcifying — a standing Awwwards gallery category, likely past peak) · Liquid Glass (death-arc risk — Apple-default → mass-copy, like glassmorphism) · View Transitions (peak-capability, emerging-adoption).