---
name: web-ui-sourcebook
description: Use when building or changing web UI - landing page, hero, pricing table, dashboard, form, modal, toast, loader, button, animation, micro-interaction, color palette, typography, spacing, or a "make it feel alive / premium / not templated" brief. A sourcebook of eight vetted open-source catalogues and libraries (EasyUI, Refero DESIGN.md, Piixpal, Stick It Up, Bencho, Amicro, Designeer, Cesaro Resources) for finding existing components, motion and design direction instead of hand-rolling them. Triggers on "component library", "micro-interaction", "animation", "motion", "hero section", "stickers", "pixel art", "design system", "DESIGN.md", "landing page", "animated", "polish", "delight".
---

# Web UI Sourcebook

Eight sources cover almost every web UI need. This skill is the routing layer over them, plus the five gates that get skipped.

**Core principle: copy before you build. Borrow the direction before you pick the palette. Every visual decision has a source; every animation has a budget.**

The failure mode this exists to prevent: an agent writes its own accordion, invents a hex palette, bolts on three hover animations, and calls it "premium". Every one of those had a source in the table below. Check the table before writing a line of component code.

## Routing table

| I need to build / decide | Go to | Why |
|---|---|---|
| Product UI: nav, data table, command menu, form, modal, chart, auth card, pricing | **EasyUI** | 134 production React components, copy-paste via shadcn, tokens included |
| One interaction no library covers (audio trim, option wheel, magnetic letters, crumple receipt) | **Bencho** | 168 live blocks, most ship full source, sorted by gesture |
| Micro-motion vocabulary: loader, shape morph, text effect, 3D card, dither chart | **Amicro** | 226 components, MIT, shadcn registry, GPU-safe defaults |
| A pixel character that lives on the page, a shader background, a whole hero | **Piixpal** | 300+ components, zero dependency, one script tag, MIT |
| A brand moment: sticker on a hero, empty state, 404, card corner | **Stick It Up** | 65 hand-drawn SVG stickers with 3D peel physics, npm |
| Colors, type scale, spacing, radius, do/don't from a real product | **Refero Styles** | 2000+ generated DESIGN.md, ready to paste as CSS vars |
| Where to find other tools, fonts, galleries, icons, portfolios | **Designeer** | 693 curated listings, every page has a `.md` twin |
| Agent skills, design systems, audio, metrics, infra, or a 2nd opinion on free tools | **Cesaro Resources** | 67 free/freemium picks across 18 categories, incl. the gaps Designeer leaves |

Two sources answer "what does this look like" (Refero, Designeer). Two are indexes of other things (Designeer, Cesaro). Four answer "what do I ship" (the rest). Use them in that order - direction, then implementation.

## Fetch machine-readable content first

Never scrape rendered HTML when a machine-readable endpoint exists. These are all verified working:

| Source | Fetch this | Returns |
|---|---|---|
| EasyUI | `https://easyui.site/llms.txt` | All 134 components with descriptions + registry URLs |
| Bencho | `https://bencho.dev/llms.txt` | All 168 blocks with one-line descriptions |
| Amicro | `https://amicro.dev/llms.txt` | Component index by category |
| Amicro (full) | `https://amicro.vercel.app/llms-full.txt` | All 226 components, one per section |
| Piixpal | `https://piixpal.dvkk.dev/llms.txt` | Install lines + rules + 137 UI components |
| Piixpal (full) | `https://piixpal.dvkk.dev/llms-full.txt` | UI components by section + 76 heroes + 121 characters |
| Stick It Up | `https://stickitupx.com/llms.txt` | Install, props, all 65 sticker names |
| Designeer | `https://designeer.xyz/llms.txt` | All 693 listings |
| Designeer | `https://designeer.xyz/{collection}.md` | One collection per file: `inspiration`, `resources`, `tools`, `portfolios` |
| Cesaro Resources | `https://resources.cesaroeduardo.com/` | No `llms.txt` or `.md` - parse the HTML, badge is in the anchor text |
| Any shadcn registry | `.../registry.json` | Machine-readable component list |

Designeer additionally returns markdown for any page when you send `Accept: text/markdown`, and every page has a `.md` twin at its address (`/resources.md`, `/tools/monospace.md`, ...). Prefer those over the HTML.

**Refero has no `llms.txt`.** Its DESIGN.md is inlined in the page's React payload. To extract one:

```js
const html = await (await fetch(`https://styles.refero.design/style/${uuid}`)).text();
const md = html
  .match(/<code class="text-foreground">([\s\S]*?)<\/code><\/pre>/)[1]
  .replace(/&lt;/g, "<").replace(/&gt;/g, ">").replace(/&quot;/g, '"')
  .replace(/&#x27;/g, "'").replace(/&amp;/g, "&");
```

Get the uuid from the grid at `styles.refero.design` (the search box filters by brand, style, color, font, or category) or from `styles.refero.design/ai-agents/design-md-examples`. Each extracted DESIGN.md is 17-26 KB.

## The eight sources

### EasyUI - `easyui.site`
React + Tailwind + Framer Motion. 134 components. Copy-paste ownership, zero configuration.

```bash
npx shadcn@latest init                                     # once
npx shadcn@latest add Surajmaurya1/easyui/magnetic-button  # source lands in components/ui/
npx shadcn@latest add Surajmaurya1/easyui/spotlight-card
npx shadcn@latest add Surajmaurya1/easyui/animated-tabs
```

Installing auto-adds `framer-motion` and `lucide-react`, and copies shared utilities into `lib/` when referenced. Registry: `https://easyui.site/registry.json`.

If you copy source by hand, provide the motion tokens yourself:

```ts
// src/lib/motion-tokens.ts
export const motionTransitions = {
  springSmooth: { type: "spring", stiffness: 300, damping: 30 },
  springSnappy: { type: "spring", stiffness: 400, damping: 25 },
  springBouncy: { type: "spring", stiffness: 500, damping: 15 },
  springGentle: { type: "spring", stiffness: 180, damping: 24 },
  easeOutCubic: { ease: [0.33, 1, 0.68, 1], duration: 0.25 },
} as const;
```

Covers: buttons, forms, auth, tables, charts (hand-written SVG, no chart lib), command menus, nav, sidebars, drawers/sheets, toasts, modals, loaders, calendars, pricing, profile cards, background effects (meteors, shooting stars, dot shader, glyph matrix), scroll effects, product tour. Design system is monochrome with elevation surfaces - it gives the component a place in a hierarchy rather than a flat card.

Use it for: everything structural. The boring, high-traffic 80%.

### Refero Styles - `styles.refero.design`
2000+ DESIGN.md generated from real product websites. Each one is a complete, AI-readable design system.

What a DESIGN.md contains:

- `## Tokens — Colors` - table of Name / Value / Token / Role. **Role is the point**: "Primary page canvas", "Primary text - warm off-white avoids the clinical feel of pure white on black", "The only chromatic color in the system - never used for filled CTAs; it is a whisper, not a shout".
- `## Tokens — Typography` - per face: weights, sizes, line-height, letter-spacing, role, and a **free substitute** (`Matter -> Inter`). Plus a `### Type Scale` with tracking per step.
- `## Tokens — Spacing & Shapes` - spacing scale, border radius, layout rules.
- `## Components` - a spec per component: nav, announcement bar, filled pill button, ghost button, product card, testimonial card, tag chip, footer.
- `## Do's and Don'ts` - explicit.
- `## Surfaces`, `## Elevation`, `## Imagery`, `## Layout`.
- `## Agent Prompt Guide` + `## Example Component Prompts` - 5 ready-to-run build prompts with exact px, weights, colors, tracking, radius, padding.
- `## Quick Color Reference`, `## Similar Brands`, `## Quick Start` (CSS custom properties and Tailwind v4 blocks).

Use it for: direction. Pick the style whose *mood* matches the product, extract the DESIGN.md, save it as `DESIGN.md` at the repo root, and build from its tokens. Copying the direction is faster and better than inventing one.

### Piixpal - `piixpal.dvkk.dev`
300+ MIT components, zero dependencies, browser-only custom elements. 16 KB shared engine + 1-4 KB per component. One shared animation loop, paused off-screen.

```html
<script src="https://cdn.jsdelivr.net/npm/piixpal@0.4/piixpal.min.js"></script>
<h1>Hello world <piix-pal pal="bitbug"></piix-pal></h1>
```

Other install paths:

```bash
# only the components you use
<script src="https://cdn.jsdelivr.net/npm/piixpal@0.4/dist/c/kitty.min.js"></script>
# no markup at all - attach by selector
<script src="…/piixpal.min.js" data-pals="bitbug@h1, boing@footer"></script>
# npm
npm install piixpal        # React: import { PiixPal } from "piixpal/react"
# shadcn
npx shadcn@latest add https://piixpal.dvkk.dev/r/NAME.json
```

Contains: 121 characters and superpowers (pals that crawl letter outlines, jellies you can throw, spiders that dangle from your nav, birds that perch on CTAs, crowds that swarm your cursor, snow that settles on letters, toasts by pigeon, a pull-chain for dark mode), 137 UI components (WebGL shader backgrounds, pixel heroes, text effects - glitch/shine/wave/melt/scan/reveal, counters, marquees, glow buttons, scene cards), and 76 complete hero templates (one self-contained HTML file each - view source, copy, edit the words).

Rules that matter: a pal lives inside the element it is placed in (or `on="css selector"`); it never blocks clicks; everything decorative is `aria-hidden` and honours `prefers-reduced-motion`; in Next.js use them from client components.

Use it for: personality and delight. Also the fastest route to a memorable hero - a template file, edited.

### Stick It Up - `stickitupx.com`
65 hand-drawn SVG stickers. npm `stick-it-up`, React 18/19 + Next 15, MIT, tree-shakeable ESM+CJS with full TS types, zero runtime CSS.

```bash
npm install stick-it-up
```

```tsx
import { FlameSticker, MetalSticker, NoriCoffeeSticker } from "stick-it-up";

<FlameSticker size={100} />
<MetalSticker size={120} peel />          // 3D vinyl peel on hover
<Sticker name="flame" size={96} peel />    // dynamic variant
```

Props: `size`, `peel` (3D tilt + lift + soft shadow), `name` (dynamic variant only), `width`, `height`, `className`, `style`, plus every standard SVG prop (`role`, `aria-label`, `onClick`). Raw SVGs are in `public/stickers/` for plain HTML.

Catalogue: 24 pixel mascots (Nori, Pip, Kuro, Mochi - each in coffee/cool/headphones/heart/laptop/rocket), 17 characters, 9 chrome and aesthetic shapes (flame, metal, star-burst, blob-burst, devil-cone, goo), 15 symbols and emblems (coffee, heart, nazar, sparkles, spiral, badge-smiley, cosmic-eye...).

Use it for: a brand moment. One sticker on a hero, an empty state, a 404, a card corner. Not for UI chrome.

### Bencho - `bencho.dev`
168 live interactive React blocks, React + TypeScript + framer-motion, MIT. Each block has its own page, a live demo, adjustable values, and usually the full source to copy.

Organised by the gesture you want, not by component name: Drag, Hover, Press, Type, Swipe, Slide, Loading, Select. That index is the feature - you arrive by saying what the user does, not what the widget is called.

Examples of what is here and is not in any library: audio trim (drag either end of a waveform), status picker (a badge on a gradient orb that opens into a row of statuses), magnetic letters, option wheel, crumpled receipt, standing calendar, orders globe, eye tracker, fold-down dropdown, escape button, pressure slider, pull-to-refresh, flute board, format bar, radial menu, ink bloom, orbital cards.

Also: **Finds** (a curated gallery of interactions by other designers, credited), **Sounds** (145 synthesised UI sounds - feedback, navigation, selection, system, movement, progress - free to use), and **Bench** (a canvas to arrange blocks side by side and tune them).

Use it for: the one interaction nobody has a library for. Search `llms.txt` by gesture.

### Amicro - `amicro.dev`
226 React micro-interaction components. React 18/19 + Motion (`motion/react`) + Tailwind v4, MIT, shadcn registry, source copied into your project.

```bash
npx @subhanhq/amicro@latest init
npx @subhanhq/amicro@latest add download-button
# or via shadcn, with the namespace in components.json:
#   "registries": { "@amicro": "https://amicro.vercel.app/r/{name}.json" }
npx shadcn@latest add @amicro/fade-up
npx shadcn@latest add @amicro/magnetic-button
```

Categories: Buttons (spring physics, magnetic hover, morphing icons, tactile press), Card Spreads (arc fans, cascades, scatter), 3D Carousels (cover flow, rotating, time machine), Loaders (120+ - iOS spinners, physics sims, morphing shapes), Shape Morphing, CSS Animations, Text Animations (diagonal reveal, blur-in words, character stagger, shimmer, typing), 3D (perspective tilt, depth layers), Mono Charts (Recharts-based), Dither Charts (retro particle dithering).

Its defaults are a good baseline and worth matching elsewhere: easing `[0.16, 1, 0.3, 1]`, 200-350 ms micro-transitions, `transform`/`opacity` only, `prefers-reduced-motion` respected.

Use it for: motion vocabulary. When you know the effect you want and need it correct on the first try.

### Designeer - `designeer.xyz`
A hand-reviewed catalogue: 693 listings in four collections plus 180 Finds (visual work from X and the web, credited to its maker). Editorial - inclusion cannot be bought, paid placements are labelled.

- **Inspiration** (145) - galleries and reading. Awwwards, 60fps (recordings of interfaces that move well), Mobbin, Details (micro-interaction analysis of top products), Hover States, navbar.design, footer.design, cta.gallery, UX Archive (historical onboarding flows), Dark Mode Design, Lapa Ninja, Pttrns.
- **Resources** (207) - Components (130): shadcn/ui, Base UI, Radix, 21st.dev, Motion Primitives, React Bits, NumberFlow, Rare UI, Fancy Components, Opensource UI, 8bitcn, coss ui, blocks.so, Evil Charts. Icons (33). Motion & 3D (28): Three.js, React Three Fiber, Drei, Blender, Book of Shaders, Shaderfrog, compute.toys, Poly Haven. Type & Colour (15): Fontshare, Fontsource, Klim, Utopia (fluid type calc), OKLCH, Ramps (OKLCH ramps), Color.review, Huetone, APCA.
- **Tools** (175) - Development (43), AI & Agents (38), Design & Canvas (25), Capture & Media (37), Productivity (27). Playwright, SVGOMG, Squoosh, Fffuel, Anim8 (video -> animated SVG), ASCII-inator, Drawably, Vercel Doctor, Hallmark, Torph, @web-kits/audio.
- **Portfolios** (182) - 59 design-engineering, 14 creative development. Emil Kowalski (Sonner, Vaul), Rauno Freiberg (Vercel Interaction Guidelines), Sarah Drasner, Jhey Tompkins, Lynn Fisher (Single Div), Gavin Nelson, Yuhang Lu, Nick Jones.

Use it for: finding the *other* tool. Designeer points at things - then you install the thing it points at. It is not a component library.

### Cesaro Resources - `resources.cesaroeduardo.com`
"Design Engineer Resources" by Eduardo de Cesaro. 77 curated entries in 18 categories, each labelled Free / Freemium / Paid. Hand-picked, inspired by designengineer.tools. **No `llms.txt`, no `.md`** - parse the page; the licence badge is the last word of each anchor's text.

```js
const h = await (await fetch("https://resources.cesaroeduardo.com/")).text();
const re = /<a href="(https?:\/\/[^"]+)"[^>]*>([\s\S]*?)<\/a>/g;
let m; const free = [];
while ((m = re.exec(h)) !== null) {
  const txt = m[2].replace(/<[^>]+>/g, " ").replace(/\s+/g, " ").trim();
  const b = txt.match(/^(.*?)\s+(Free|Freemium|Paid)$/);
  if (b && b[2] !== "Paid") free.push({ name: b[1], url: m[1], badge: b[2] });
}
// 67 entries: 31 Free + 36 Freemium. 7 Paid excluded.
```

**Free / Freemium entries only (67), by category:**

- **Web References** - 60fps, Inspora, Collect UI, UI Land, Awwwards
- **Images** - Lummi, Cosmos, Imageory, Pixabay
- **Components** - 21st.dev, shadcn/ui, HeroUI, Origin UI, aMicro Loaders, TUFFUI, Libraries.dev, Details
- **Icons** - Phosphor Icons, Hugeicons, Feather Icons, Pixelarticons
- **Fonts** - Google Fonts, Fontshare
- **Motion** - Motion In Design, LottieFiles, Transitions.dev, GSAP, Motion, Metalforge
- **Design Systems** - getdesign.md, components.design, Design Systems Brasileiros, Design Systems Directory
- **Agent Skills** - Anti Web Slop, Revenue Centric Design, Pocket Design MCP, /grill-me, /caveman
- **AI Tools** - Weave, Grokbot, Cursor, Agentes.chat
- **Design Tools** - FigJam, Figma, Elyx
- **3D Tools** - Spline, Blender, Three.js, Tripo3D
- **Web Utility** - SVGOMG, OpenShaders, Coolors
- **Video & Capture** - OpenScreen, NVIDIA Broadcast, OBS Studio, UltraMock, DaVinci Resolve
- **Infrastructure** - Vercel, Supabase, Sanity
- **Audio** - ElevenLabs, Pixabay Sound Effects
- **Metrics** - Mixpanel, Himetrica, PostHog
- **Organization** - Notion, Linear

Excluded (Paid): Mobbin, MJ Pro, MyFonts, Plau, PintassilgoPrints, Screen Studio, Hostinger.

Use it for: the five categories the other seven do not cover - **Agent Skills, Design Systems, Audio, Metrics, Infrastructure** - and as a second opinion on free tools. It overlaps Designeer heavily (60fps, Awwwards, shadcn, 21st.dev, Phosphor, Fontshare, Motion, Transitions.dev, GSAP, Three.js, SVGOMG, Figma), so check Designeer first for those. Same rule as Designeer: it points at things; check the licence of the thing itself before shipping it.

## Build order

Work in this order. Skipping to step 4 is why the result looks generic.

1. **Direction.** Pick a style in Refero that matches the product's mood. Extract its DESIGN.md. Save it as `DESIGN.md` at the repo root.
2. **Foundations.** Turn the DESIGN.md tokens into CSS custom properties and a Tailwind v4 `@theme` block. Nothing gets a raw hex after this step.
3. **Structure.** Assemble the page from EasyUI. Nav, hero layout, features, pricing, footer.
4. **Motion.** Add the micro-interactions from Amicro, and pull in a Bencho block for the one distinctive interaction.
5. **Personality.** One Piixpal character or one Stick It Up sticker, where it earns its place. Then stop - two is a theme, three is noise.
6. **More tools.** Designeer first, Cesaro Resources second, if the job needs a font, an icon set, a shader, an agent skill, an audio bed, or an asset pipeline.
7. **Gates.** Run the five gates below. Then verify in a browser.

## The five gates

### Gate 1 - Accessibility and reduced motion

Non-negotiable. Cut here and the work is not shippable.

- **Contrast**: WCAG 2.1 AA - 4.5:1 body text, 3:1 large text and UI component boundaries. Check against the *actual* rendered background, not the token name - a mid-gray token on a dark surface is not 4.5:1 just because both are named "gray".
- **Focus**: never `outline: none` without a visible replacement. 2px ring with 2px offset, `:focus-visible`, on every interactive element.
- **Targets**: 44x44px minimum. Enlarge small visual targets with a pseudo-element hitbox, not by growing the layout.
- **Keyboard**: everything reachable and operable. Modals trap focus and return it to the trigger on close. Menus and comboboxes follow the ARIA pattern.
- **Names**: every control has an accessible name. Decorative = `aria-hidden` and `pointer-events: none`. Never `aria-hidden` on a focusable element.
- **Async**: `aria-live` for status changes; `aria-busy` while loading.

**Reduced motion is a global kill switch, not a per-component checkbox.**

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

Then go further for decorative motion: parallax, autoplay, confetti, marquees, floating loops, shader backgrounds all off. Keep state-change feedback but reduce it to a short opacity change - the user still needs to know what happened, they just do not need the choreography. In Motion, use `useReducedMotion()` from `motion/react` and branch on it.

### Gate 2 - Performance budget

The budget is the frame: **16.7 ms at 60 Hz, 8.3 ms at 120 Hz.** Design for 8 ms.

- Animate `transform` and `opacity`. `width`, `height`, `top`, `left`, `margin`, `padding` trigger layout - one growing element can re-lay out the page every frame. Use `scale()` and `translate()` instead.
- Never `transition: all`. Name the exact properties. `all` animates whatever changes next, including layout properties nobody intended.
- Never animate a CSS custom property on a parent - they inherit, so every descendant recalculates styles each frame. Write `transform` on the moving element.
- Never `setState` per pointermove, per scroll, per frame. Write `ref.current.style.transform`, or use a MotionValue. A re-render every 16.7 ms blows the budget by itself.
- Animated `blur()` costs a lot, especially in Safari. Keep it under 20 px, 2-5 px is usually enough.
- `will-change` promotes a layer and costs GPU memory. Add it only after you see the 1px hand-off shift, only on the element that animates, never on `*` or a large container.
- Layout animations (Motion `layout` / `layoutId`) run on the main thread. Do not run one during a route transition or hydration - that is exactly when the main thread is busy.
- Prefer CSS transitions and WAAPI when the page is busy (navigation, data fetch) - they can be hardware-accelerated off the main thread.
- Particles: canvas, not DOM nodes.

**Verify on a real device.** Chrome DevTools Performance with 4x CPU throttle approximates a mid-range phone. A MacBook hides almost every problem on this list. "Smooth on my machine" is not evidence.

### Gate 3 - States and robustness

Every surface that shows data has four states. Shipping only the happy path is the most common miss.

| State | Requirement |
|---|---|
| Loading | Skeleton matching the final layout, or a spinner. No layout shift. If the load resolves in under 300 ms, show nothing - a spinner that flashes reads as a glitch. |
| Empty | Say what would be here and offer one action. Never a bare "No data". |
| Error | Say what failed, in the user's terms, and offer a retry. |
| Success | Confirm the change happened. |

Robustness checks:

- **Long content.** Test with the longest realistic string - a 60-char SKU, a German compound noun, an untranslated API value. Truncate names, wrap sentences. Never a fixed-width label next to a variable one.
- **Numbers that change.** `font-variant-numeric: tabular-nums` so digits do not reflow the layout while counting.
- **Images.** Always `width`/`height` or `aspect-ratio` so nothing shifts on load.
- **RTL.** Use logical properties (`margin-inline`, `padding-inline`, `inset-inline-start`), never `left`/`right`.
- **i18n.** No text baked into images or SVG `<text>`. Allow ~1.3x expansion. Do not concatenate sentences - use placeholders with a real i18n function.
- **Touch.** 44px targets, `touch-action` set on the drag axis, and hover effects gated behind `@media (hover: hover) and (pointer: fine)` - touch fires phantom hovers on tap.
- **Zoom.** Must work at 200% browser zoom. Do not lock container heights.
- **Dark mode.** Both themes designed, not one theme inverted. Use semantic tokens (`--surface-raised`), not raw colors, so both themes work.
- **Persistence.** Remember user preferences - reduced motion, sound on/off - in local storage.

### Gate 4 - Design direction and DESIGN.md

The DESIGN.md is the contract the whole build obeys. If the file does not exist, create it before writing components - not after.

A working DESIGN.md covers:

- **Surfaces** - canvas, content surface, elevated surface, each with a reason.
- **Color by role** - canvas, content surface, readable text, selected state, action. Roles, not hexes, are what transfers.
- **Typography** - the scale with size, line-height and tracking per step; which face owns display and which owns reading; the maximum paragraph width (60-75 characters).
- **Spacing and layout** - the scale, the grid, the section rhythm.
- **Motion** - durations, easings, spring presets, and what does not animate.
- **Imagery** - scale, crop, surrounding whitespace, and what imagery is for.
- **Copy** - voice and the words used for each state.
- **Do's and Don'ts** - the explicit prohibitions.

Rules of the file:

- No raw hex in a component. Always a token.
- Every rule states a value and a why: "Raised surfaces use `shadow-raised`, never a solid border. Why: a solid border makes a raised surface look flat and heavy; a semi-transparent shadow adapts to both themes."
- A check beats a sentence. If a violation is detectable by matching code - a banned value, a raw hex, a forbidden property - make it a lint rule or a test. A sentence in a long file gets skipped.
- Point to it from `CLAUDE.md` or `AGENTS.md`, or no agent will read it: `Before building or changing UI, read DESIGN.md.`

When you derive the direction from a Refero style, keep the whole thing - the do's and don'ts and the example component prompts are the parts that survive translation.

### Gate 5 - Delivery - licences and verification

**Licences.** EasyUI, Amicro, Piixpal, Bencho and Stick It Up are MIT - read the repo `LICENSE` to confirm the version you vendored. Designeer and Cesaro Resources are catalogues of pointers: check the licence of each thing they point at (Cesaro labels Free / Freemium / Paid - "Freemium" still has limits, read them). Refero DESIGN.md files describe real brands - the fonts are named with a **free substitute**; use the substitute, not the original. Do not ship a brand's logo, and do not hotlink their imagery. Use assets you own or CC0.

**Verification.** Before saying the work is done:

1. Run the app. Open it in a browser.
2. Console is clean - no errors, no warnings, no React key warnings, no hydration mismatch.
3. Keyboard pass: tab through the whole page, open the menus and modals, close them, check focus returns to the trigger.
4. Reduced motion: emulate `prefers-reduced-motion: reduce` and confirm nothing decorative still moves.
5. Widths: 375px and 1440px, no overflow, no stacked-and-broken hero.
6. Screenshot each state - default, hover, focus, loading, empty, error - and look at them.
7. Lighthouse accessibility score, and read the failures rather than the number.
8. Performance panel, 4x CPU throttle, confirm no long tasks during the animations.
9. Every gate above, checked against the actual build.

Never report a page as working because the code looks right. Code that looks right is the most common way it is wrong.

## Worked example

Brief: a B2B analytics SaaS landing page that feels alive and premium, not templated. Next.js 15 + Tailwind v4 + Motion.

**Step 1 - direction.** In Refero, search a dark, terminal-flavoured developer tool. Extract the DESIGN.md (Warp is the reference: obsidian canvas, warm off-white text `#faf9f6`, one violet accent `#cbb0f7` used only for icons and code, pill buttons 33 px radius, tight tracking -0.04em at display sizes, surfaces rising through `#000000 -> #121212 -> #1e1e1d -> #333333`). Save as `DESIGN.md`.

**Step 2 - foundations.**

```css
:root {
  --color-canvas: #000000;
  --color-surface: #121212;
  --color-raised: #1e1e1d;
  --color-border: #333333;
  --color-text: #faf9f6;
  --color-text-muted: #868684;
  --color-accent: #cbb0f7;
  --radius-pill: 33px;
  --text-display-lg: 56px; --leading-display-lg: 0.96; --tracking-display-lg: -2.24px;
  --ease-out: cubic-bezier(0.16, 1, 0.3, 1);
  --duration-micro: 200ms; --duration-panel: 300ms;
}
```

**Step 3 - structure.** EasyUI for the chrome that must be reliable: `AnimatedTabs` for the pricing period switcher, `CommandMenu` for the ⌘K palette, `SpotlightCard` for feature cards, `StatCard` for the metrics row, `Sheet` for the mobile nav, `PriceChart` for the growth panel. `npx shadcn@latest add Surajmaurya1/easyui/spotlight-card`.

**Step 4 - motion.** Amicro for the micro-motion: `magnetic-button` on the primary CTA, `tilt-card` on the feature cards, a `morph-loader` while the dashboard numbers load, `apple-text-reveal` on the hero headline. One Bencho block for the distinctive interaction - `status-picker` for the plan badge, or `option-wheel` for a "which metric do you care about" selector, because nothing in any library does that.

**Step 5 - personality.** One thing. A Piixpal sprite inline in the empty state of the demo panel (eyes follow the cursor - `piixpal.min.js`, `<piix-sprite name="mochi">`), because it is where the user is already looking while waiting for data. Nothing else on the page.

**Step 6 - gates.**
- Contrast: `#868684` on `#000000` is 5.76:1 - passes for body. `#cbb0f7` on black is 11.09:1 - passes, but it stays out of filled CTAs per the style's own rule.
- The tilt card animates `transform` only, 200 ms, `transform-origin: center`. The morph loader is canvas.
- `prefers-reduced-motion` disables the tilt and the loader's rotation, keeps the text reveal as a fade.
- The demo panel has all four states: loading (morph loader), empty (the mochi sprite plus "Run a query to see results"), error ("Query failed - retry"), success (numbers with `tabular-nums`).
- Verified in the browser at 375 and 1440, console clean, keyboard pass done, Lighthouse a11y checked.

That is a premium page, and every piece of it came from a source.

## Common mistakes

- **Hand-rolling a component that exists.** Check the routing table first. Every one of these eight has the thing.
- **Scraping rendered HTML** when `llms.txt`, `.md`, or `registry.json` exists.
- **Copying a style's colours without its rules.** A palette without its do's and don'ts produces exactly the generic result you were trying to avoid.
- **Treating "DESIGN.md" as a palette dump.** The role column and the prohibitions are the value.
- **Adding motion at the end.** Motion is a design decision made during step 3-4, not a polish pass.
- **Decorating everything.** One memorable thing beats five forgettable ones.
- **Animating layout properties** because the tutorial did.
- **Calling `setState` in a pointer handler.** One re-render per frame, budget gone.
- **Shipping only the happy path.** Loading, empty and error are the states users hit first.
- **Hardcoding a hex** "just once". It becomes the second hardcoded hex.
- **Saying "done" without opening the browser.**
