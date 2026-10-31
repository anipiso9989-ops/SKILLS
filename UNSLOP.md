---
name: unslop
description: >-
  Audit and refine an existing website, app, dashboard, or landing page so it feels
  intentionally designed rather than generically AI-generated. Preserve product
  behavior and brand identity; improve visual hierarchy, spacing, color, icons,
  copy, typography, layout, data presentation, imagery, motion, responsiveness,
  and accessibility. Use for requests to unslop, polish, clean up, critique,
  or redesign an existing interface, including code, a live URL, or a screenshot.
license: CC-BY-4.0
metadata:
  original_author: Damir Sokolovsky
  original_repository: https://github.com/heydamir/unslop
  adaptation: self-contained, implementation-focused edition
---

# Unslop — a self-contained UI refinement skill

Adapted from **unslop** by **Damir Sokolovsky** (https://github.com/heydamir/unslop), licensed **CC BY 4.0**. Retain this attribution when sharing adaptations. No external instruction files, example files, scripts, or reference images are required to use this skill.

## Mission

Turn an existing interface into a **coherent, distinctive, functional design**. Keep what the product does; improve how clearly, credibly, and memorably it communicates.

AI-looking UI commonly spreads attention across violet gradients, glows, emoji labels, oversized pills, arbitrary spacing, vague claims, repeated cards, and gratuitous animation. Removing those patterns is not the objective by itself: **replace accidental decisions with a system grounded in the product, its audience, and its actual content.** A sterile generic template is not a success.

**Decision priority, highest first:**

1. Explicit user constraints and established brand identity.
2. Feature completeness, content accuracy, interaction behavior, and technical constraints.
3. Accessibility, legibility, responsive usability, and performance.
4. Clear hierarchy, visual consistency, and product-specific character.
5. Optional decoration and novelty.

These are **diagnostic heuristics, not bans on styles**. Purple, gradients, bold type, illustration, pills, dense data, or animation can be correct when they are intentional, brand-consistent, accessible, and serve a purpose. Do not replace an authentic brand with a fashionable monochrome template. Do not force a dramatic redesign of a screen that only needs polish.

## Operating contract

- **Preserve:** product name, logo, feature set, workflows, navigation destinations, content meaning, important section order, states, API contracts, and the project's framework and conventions, unless the user authorizes changing them.
- **Inspect before editing:** existing design tokens, components, fonts, logo/assets, responsive rules, color modes, and representative screens. Prefer the project's system over new dependencies or arbitrary one-off values.
- **Never fabricate:** capabilities, testimonials, customer counts, performance metrics, case studies, charts, or real user data. For an explicitly illustrative mockup, use plausible **clearly labeled sample data**, never imply it is production data.
- **Favor minimal diffs:** remove redundant decoration and fix the cause in shared components/tokens before patching instances. Avoid unrelated refactors or swapping stacks.
- **Don't require external resources:** this file contains the necessary criteria, defaults, visual recipes, and QA process. Use tools already available in the environment; do not assume an external script, reference directory, image-generation service, or icon package exists. Add a dependency only when necessary and allowed by the user.
- **Respect scope:** audit-only means no edits. Polish means focused repairs. Full redesign means more extensive composition changes while preserving the product. Never silently turn a screenshot mockup into a claimed implementation of an inaccessible live application.
- **Be honest about evidence:** distinguish what you rendered and tested from what you inferred from source code. Do not claim a page is responsive, accessible, or working without checking the relevant behavior.

## 0. Inspect, audit, calibrate

### Obtain a baseline

1. **Local code:** find the actual route/page and the components and styles that render it. Read shared styles and existing assets. Run the project using its normal commands when feasible, and capture the current UI at a representative desktop and mobile viewport using an available browser tool. Inspect real rendering rather than relying solely on class names.
2. **Live URL but no source:** inspect the live page and its responsive states if browsing is available. If the environment permits saving HTML/CSS/assets, work on a local **static approximation**; otherwise provide a precise audit and implementation guidance. A saved page can omit JavaScript behavior, authentication, fonts, animations, or dynamic content. Clearly label any reconstruction as non-authoritative and not a deployable replacement for the original app.
3. **Screenshot only:** audit what is visible. If implementation is requested, build the best faithful baseline possible in the requested stack before refining; clearly identify what cannot be inferred (behavior, content below the fold, breakpoints, hidden states). Do not invent missing features.
4. **No visual tool available:** inspect source, derive a bounded improvement plan, and disclose that visual verification was unavailable.

### Make a location-specific slop audit

For each issue, record **location → observed problem → user impact → proposed correction**, then classify it as **critical** (broken, unreadable, misleading, inaccessible), **major** (unclear hierarchy or systemic inconsistency), or **minor** (polish). Only report issues supported by the screen or code.

Check these categories:

| Area | Common tell | Better diagnostic question |
| --- | --- | --- |
| Surfaces | Multihue gradients on every card; neon borders; glass-plus-glow; large blurred color blobs | Does a surface treatment establish hierarchy or merely attract attention? |
| Color | Five nearly identical tints; each item accented; illegible muted text | Can each color be explained by a role, state, or data meaning? |
| Type | Heavy 800–900 headings everywhere; tracked uppercase micro-labels; competing sizes; tight small text | Can the reader identify primary, secondary, and supporting information instantly? |
| Icons | Emoji navigation; mixed icon families, colors, sizes, or strokes; unnecessary icons | Does each icon improve recognition, and do labels line up? |
| Spacing | Random 7/11/13px gaps; uneven card padding; unrelated items crowded together | Is there a repeatable rhythm and clear proximity-based grouping? |
| Copy | “Supercharge”, “unlock”, “AI-powered” as filler; greetings as titles; unqualified +12%; vague buttons | Does the text accurately explain what happens, for whom, and compared with what? |
| Components | Every item a pill; redundant badges; identical stat cards with decorative sparklines | Does each component help accomplish or understand something? |
| Data | Rainbow single-series bars; fake metrics; charts selected for visual effect | Can someone read the comparison, trend, magnitude, and source of data? |
| Layout | Generic centered stack; enormous empty bands; cramped app shell; poor mobile ordering | Does composition prioritize the actual task and adapt to the viewport? |
| Visuals | Glossy orb, holographic brain, stock robot, unrelated abstraction, fake UI baked into images | Does the visual demonstrate the product or clarify its world? |
| Motion | Loops, parallax, pulsing labels, bouncing cards, `transition-all` | Does motion communicate a state or spatial relationship? |
| Usability | Lost focus state, tiny target, clipped text, nonfunctional control, broken contrast | Can users operate the screen by mouse, keyboard, touch, and assistive technology? |

**Select intensity from the evidence:**

- **No significant issues:** say so, suggest up to three precise refinements, and stop unless edits are requested.
- **Polish:** fix the observed problems; retain the existing layout, font, palette, imagery, and brand direction where sound.
- **Redesign:** when problems are systemic, establish a direction and make coordinated changes across the screen.

### Define the product-specific direction

Before a substantial redesign, write five terse lines based on the product, not current design trends:

- **Product:** what it does, for whom, and the main job on this screen.
- **Character:** 2–3 specific traits, e.g. “measured, technical, reassuring” rather than “modern, sleek, premium.”
- **World:** a relevant place, material, instrument, or domain metaphor that informs the palette and imagery; do not apply the metaphor literally everywhere.
- **Moments:** zero to two areas where detail or richer color genuinely rewards attention, e.g. an important chart, a real product preview, a tangible object, a meaningful illustration.
- **Signature:** one restrained, repeatable detail particular to this product, e.g. a route line for deliveries, a keycap pattern for a developer tool, or an incident status motif.

Existing brand assets and conventions constrain this direction. If the brand is already coherent, refine it rather than inventing a new aesthetic. Present the audit and direction **before editing only when the user requested approval or when a major, ambiguous brand/product decision needs authorization**; otherwise implement the requested work and report the direction afterward.

## 1–9. Refine the interface in ordered passes

Apply each pass **across the scoped screen**, not to isolated components at random. Passes are a reasoning order; edits can be implemented together when that is cleaner. When the screen is already strong, run only the relevant passes.

### Pass 1 — Spacing and alignment

- Establish a **4px-based rhythm**: useful starting values `4, 8, 12, 16, 20, 24, 32, 40, 48, 64, 80, 96, 128`. Two-pixel optical corrections are allowed; exact centering and inherited brand grids take precedence over blind rounding.
- Reuse the same height, padding, and gap for equivalent rows and components. Make **within-group spacing smaller than between-group spacing**. Align icons, labels, input baselines, and numeric columns.
- Default ranges, adjust for density: control interior `4–12`; related rows `2–8`; between groups `12–24`; page edges `24–32`; cards `20–24`; sections `32–64` in apps or `80–128` on spacious landing pages.
- App navigation: row height `32–36` compact, `40–44` for comfortable touch contexts; gap `2–4`; section heading roughly `24` above and `8` below. Preserve larger hit areas as needed.
- Find the source of inconsistent spacing in shared tokens/components. Remove accidental negative margins, transforms, and one-off pixel values rather than layering compensatory offsets.

### Pass 2 — Color and surface hierarchy

- Prefer **one coherent neutral ramp** for canvas, raised surfaces, borders, and text. Tint subtly toward the product's world without turning a data-heavy app into a large saturated color field. Landing pages may be more expressive.
- Give text three dependable roles: **primary, secondary, muted**. Keep muted text for captions and supporting information, and verify it is readable. Use semantic success/warning/error colors for actual states, not ornament.
- Use an accent **selectively**, e.g. main action, active selection, or focal metric; a chart may use multiple hues when distinct data series require them. Preserve legitimate brand colors.
- Remove unjustified colored shadows, glowing outlines, gradient type, visual fog, and glass stacking. Use subtle surface steps and hairline borders for depth; quiet shadows are acceptable on light surfaces or floating objects.
- Contrast targets: **WCAG AA** normal text at least `4.5:1`, large text at least `3:1`, and important graphical controls/focus boundaries at least `3:1` against adjacent colors where applicable. Validate the actual rendered foreground/background pairs and all interaction states; do not assume a sample palette guarantees contrast.

### Pass 3 — Icons

- Reuse the project's icon family; if none exists, use simple consistent inline SVG or an already available set. Do **not** add a package merely to change icons.
- A good starting point is `16–18px` icons, `1.5` stroke, `8–10px` icon-to-label gap, and a fixed icon column. Sizes may differ deliberately for primary actions, accessibility, and touch contexts.
- Replace decorative emoji, mismatched glyphs, and duplicate pictograms with a meaningful icon or nothing. Keep meaningful symbols and branded iconography when justified. Icons must not be the only cue for a status or action.

### Pass 4 — Copy and information integrity

- Write specific headlines that describe the product or task. Prefer a page title, period, or meaningful user context over generic greetings. Replace “magic”, “seamless”, “revolutionary”, “supercharge”, or “AI-powered” filler with observable benefits or functions.
- Preserve established terminology and the user's factual content. Use concise action labels that name the action, e.g. “Create invoice”, not an ambiguous “Get started” when context is absent.
- Express deltas with comparison periods and units: `+12% vs previous month`, not just `+12%`. Keep units, currency, number precision, and dates consistent.
- Make short UI labels sentence case by default, avoid unnecessary exclamation marks, and omit terminal periods on buttons/headings. Do not mechanically rewrite a brand's voice or alter legal/regulatory text.
- Verify or remove dubious social proof, fabricated quotes, unsupported speedups, and illustrative data presented as real. When sample values are necessary, label the view as a demo or mockup.

### Pass 5 — Typography

- Keep a deliberate existing type system. Otherwise, pick **one readable interface family** and at most one complementary mono or display face. Suggested directions: precise (`Geist`, `IBM Plex Sans`, `Inter`), warm (`Instrument Sans`, `DM Sans`, `Figtree`), editorial (interface sans plus `Newsreader`/`Instrument Serif` for large headlines), playful (`Space Grotesk`, `Bricolage Grotesque`). Use installed/local fonts when possible; fall back cleanly.
- Establish hierarchy primarily with **size, spacing, and color**, then weight. Typical UI weights `400–600`; stronger weight is allowed for actual brand/display needs. Avoid heavy headings everywhere, italic emphasis everywhere, and excessive letter-spacing on small text.
- Use tabular numerals for columns and metrics. Keep line length manageable and do not force a headline onto one line at small widths. Use sensible line-height, particularly for body copy and localization.
- Starting scale (px unless noted):

| Role | Size | Line-height | Typical weight |
| --- | --- | --- | --- |
| Metadata | 12–13 | 16–18 | 400–500 |
| Captions, secondary | 13–14 | 20 | 400 |
| Body and navigation | 14–16 | 20–24 | 400–500 |
| Brand wordmark | 16–18 | ~1.1 | 500–600 |
| Page title | 28–36 | ~1.15 | 400–600 |
| KPI | 28–36 | ~1.0–1.15 | 500–600 |
| Hero subheading | 18–20 | 28–30 | 400 |
| Hero headline | 56–72 desktop; 36–44 mobile | 1.02–1.12 | 400–600 |

Tracking defaults to `0` under `16px`; large display sizes may use `-0.02em` to `-0.03em` if legible. Treat values as starting points, not mandatory overrides.

### Pass 6 — Layout and responsiveness

- **Preserve information architecture and interactions.** Adjust alignment, width, whitespace, and ordering only to make the existing task clearer. Do not remove important sections or controls for symmetry.
- **Apps/dashboards:** maintain realistic full-width application proportions, often sidebar `240–280px`, header `56–64px`, flexible main content and reasonable max width `1440–1600px`. Do not shrink a working app into a narrow decorative mockup.
- **Landing pages:** make the above-the-fold message and proof legible together. Choose a split hero, a credible product preview, or text over a thoughtfully composed scene **based on the content**—not because a particular hero pattern is fashionable. Give the main proof enough area; avoid dead empty bands and stacked promotional chips.
- Keep one clear primary CTA and at most one supporting CTA in a hero when appropriate. Preserve other essential navigation, pricing, disclosure, and compliance actions elsewhere.
- On narrow screens, reflow in a logical reading order; simplify dense charts/tables with accessible alternatives or horizontal scrolling where needed; avoid scaled-down desktop previews with microscopic type. Preserve nav access and visible actions.
- Test at approximately `320–375px`, `768px`, `1280–1440px`, and an expanded desktop width when tools permit. Verify content at zoom, longer text, and both color modes if supported. Breakpoints follow actual content failure, not arbitrary device labels.

### Pass 7 — Components, data and detail

- Use a coherent corner-radius system, e.g. keycaps `4–6px`, controls `8–10px`, cards `12–16px`, larger panels `14–20px`. In nested surfaces, `inner radius ≈ max(4px, outer radius − inset)` is a useful optical starting point, not a geometric law.
- Make active, hover, pressed, disabled, loading, empty, error, and focus-visible states legible and functional. Keyboard focus must be visible. Support descriptive labels, proper semantics, and screen-reader status for important updates.
- Prefer quiet counts and metadata to glowing badges. Use badges only for real status, plan tier, or release information; use keycap shapes for shortcuts. Keep signature details sparse and meaningful.
- **Stat cards:** start with label, important figure, and a defined comparison. Do not add mini-charts merely to fill space. A small chart is appropriate only if its trend is relevant and interpretable.
- **Charts:** choose trend → line; comparison → bars; composition → stacked bar or ranked list; single target → progress indicator. Use a muted grid and labels, consistent axes and formatting, accessible legends, and distinguishable series. Highlight the series that matters; do not use color alone to encode data. Never replace real data with attractive invented curves.
- Prefer hierarchy over a wall of equally prominent cards. A wide primary chart (often `280–360px` tall) with supporting stats and a narrower table/list may work better than several competing charts. Use the data and task to decide.
- Accessible interactive controls should meet WCAG 2.2 target-size requirements where applicable (at least `24×24px` or an allowed spacing exception; larger targets are preferable for touch), have a visible label/name, and retain usable contrast.

### Pass 8 — Product-specific visuals

**First decide whether any new visual is necessary.** Most dashboards, settings pages, and dense apps do better with well-rendered content than illustrative art. For a marketing hero, onboarding, or empty state, a visual should support a **one-sentence truthful claim about the product**.

**Selection hierarchy:**

1. Real screenshots, screenshots composed from existing UI, authentic product images, or data visualizations already available.
2. A product preview built from real components or clearly labeled demo data showing the promised workflow.
3. An image grounded in the product's physical or conceptual world, when it adds information or character. Generate only when an image tool is available and use is authorized; confirm any paid generation before running it. Avoid requiring an image service for ordinary refinement.

A scene should have **one clear subject**, restrained materials/color, and negative space for text if text will overlay it. A product object can be a studio-lit card, device, package, page, or tool that actually belongs to this product. Avoid default glossy orbs, holographic brains, liquid chrome, floating neon geometry, and generic robot imagery **unless genuinely relevant**. Do not bake interface text, logos, or fake buttons into the image.

**Prompt formula, if an image is justified:**

> `[Concrete subject and meaningful product connection]; [simple setting]; [light direction and exact palette/background]; [one consistent visual medium]; [subject placement and negative space for layout]; [wide/portrait aspect ratio and intended crop]; no text, letters, logos, UI, or watermark.`

Possible visual idioms and when to choose them: a restrained cinematic photo for trust and tangible products; softly painted scenery for a warm narrative; matte stylized 3D for playful products; a monochrome engraving for heritage/editorial systems **only when type and page treatment match**; a studio product render for physical workflows; a code-based chart or literal product demonstration for technical tools. These are compositional principles, not designs to copy.

**Integrate deliberately:** match image edges to the surrounding surface; crop responsively; keep HTML text sharp and selectable; provide useful alt text (or empty alt when purely decorative); specify dimensions to avoid layout shift; optimize format/size and loading priority; save generated assets into the project when possible. Avoid an empty image slot in a final deliverable—use a strong real product preview instead.

**Optional native-to-brand image treatments** (only for a primary visual, never as filler):

- **Duotone:** map grayscale luminance between a dark neutral and the brand accent/ink.
- **Halftone:** sample luminance onto a regular dot grid, with radius proportional to brightness.
- **Dither:** quantize to two or three brand colors using ordered or error-diffusion dithering once on load.
- **Glyph image:** sample an image on roughly `6–10px` cells, map brightness to characters related to the product (currency marks for finance, braces for developer tooling, musical notation for music). Use a mono font and create all image/sample data once.
- For canvas effects, **never** call `getImageData` every animation frame; precompute cell samples/atlases, update only a few cells at `8–12fps` if motion is essential, pause when offscreen, and cap rendering density (e.g. device-pixel ratio ≤2). Prefer a static result for reduced motion.

### Pass 9 — Motion

- Remove arbitrary pulsing, floating, shimmer, scroll parallax, zoom-on-card-hover, spring overshoot, and attention-seeking loops.
- Motion should communicate causality: hover/focus feedback, a pressed state, a menu appearing next to its trigger, an important data transition, or one restrained entrance.
- Animate named properties such as `color`, `background-color`, `border-color`, `opacity`, and `transform`, not `transition-all`. Avoid layout-changing `width`, `height`, `top`, `left`, or expensive large blurs where possible.
- Starting curve: `cubic-bezier(0.22, 1, 0.36, 1)`. Typical durations: feedback `120–180ms`; popovers `160–220ms`; larger entrance transitions `300–500ms` only if justified; any stagger `40–60ms` for at most a few elements. A subtle translate is usually `6–12px`; press-scale `0.97–0.98` only when it reads correctly.
- Honor `prefers-reduced-motion`; disable nonessential movement and keep state changes understandable without motion. Never use animation as the sole indicator of information.

## Embedded design tokens — optional starting systems

**Do not overwrite a mature design system with these.** They are convenient defaults when the project has none. Use a single ramp consistently, adjust it to the brand, and recompute contrast with the actual interface.

| Role | Dark graphite | Dark warm | Dark ink | Light graphite | Light warm | Light ink |
| --- | --- | --- | --- | --- | --- | --- |
| Deepest / sidebar | `#0f0f10` | `#12110f` | `#07080d` | `#f4f4f5` | `#efe9df` | `#eceff4` |
| Page | `#141415` | `#161513` | `#0b0d14` | `#fafafa` | `#f7f4ee` | `#f5f7fa` |
| Card | `#19191b` | `#1c1a18` | `#11141c` | `#ffffff` | `#fdfbf7` | `#ffffff` |
| Raised / active | `#232325` | `#262421` | `#1a1e28` | `#f4f4f5` | `#efe9df` | `#eceff4` |
| Primary text | `#ededed` | `#efe9de` | `#eef0f5` | `#171717` | `#1f1b16` | `#111827` |
| Secondary text | `#a1a1a6` | `#aaa398` | `#a3a9b8` | `#525252` | `#5e554a` | `#4b5565` |
| Muted/meta | `#8c8c93` | `#928c82` | `#818ba0` | `#6e6e6e` | `#70665a` | `#606b7d` |

Default hairline: text or neutral ink at approximately **6–10% opacity**, validated on its actual background. Dark UIs derive most depth from surface steps; light UIs may use a soft `0 1px 2px rgba(0,0,0,.05)` or, for floating elements, `0 8px 24px rgba(0,0,0,.06)`. A quiet inset top highlight is acceptable when it adds legibility.

Brand accent: choose an existing authentic color; otherwise choose one from the world—amber/coral for warm domains, cobalt/teal for technical or aquatic domains, moss/sage for natural domains. An accent is a **role**, not a mandate to repaint every component. Text or icon contrast must pass for each usage. Semantic positive/negative colors should also be contrast-tested rather than copied blindly.

Default buttons: common in-app controls `32–40px` tall when density permits, primary hero CTAs `44–48px`, horizontal padding `16–20px`, clear primary/secondary hierarchy. Input, list, and CTA dimensions should be adjusted for target-size accessibility and the project's existing component standards.

## Verification — required before declaring success

Use available browser/test tooling and iterate as needed; **do not impose an arbitrary render limit**. Compare before and after at equivalent viewports, content, and theme. Visual elegance does not excuse broken behavior.

### Visual and content checks

- [ ] Original product identity, information meaning, important section order, and functions are intact.
- [ ] Audit findings are fixed at their locations, and changes fit the stated product direction.
- [ ] Consistent spacing, alignment, typography, radii, icon language, and neutral hierarchy.
- [ ] Accent, strong color, rich imagery, and special treatments serve identifiable purposes.
- [ ] Headlines and CTA labels are specific; numbers and comparisons are consistent; no invented proof or real-looking fictitious production data.
- [ ] No new generic-template symptoms: interchangeable headline, unrelated imagery, redundant badges, arbitrary empty space, or decorative visual noise.
- [ ] The most important information is evident within seconds; nothing has been hidden merely for aesthetics.

### Functional and accessibility checks

- [ ] Navigation, forms, links, controls, data display, and responsive menu still work.
- [ ] Desktop, tablet, and mobile have no avoidable clipping, horizontal overflow, unreadable data, or broken reading order.
- [ ] Text and component contrast are measured; keyboard traversal/focus is visible; control names and labels are meaningful.
- [ ] Interaction states (hover, focus, disabled, loading, error, empty) remain usable; touch targets and keyboard interactions are acceptable.
- [ ] Reduced-motion preference works where there is motion; imagery has appropriate alternative text; no unnecessary layout shift or large performance regression.
- [ ] TypeScript/build/lint/tests pass **where the project supports them**, and remaining failures are disclosed rather than concealed.

### The identity test

Ask: **If the product name and logo were changed, would this page fit almost any other product?** If yes, the work may be tidy but still generic. Improve the content-specific proof, composition, typography, and signature detail without adding decorative noise.

## Delivery format

When implementation is requested, **make the changes in the user's actual project** where access allows. Report concisely:

1. **Scope and diagnosis:** what was inspected, what was wrong, and how extensively you changed it.
2. **Direction:** the five product-grounded lines, compactly.
3. **Changes:** the meaningful before → after decisions, grouped by the nine passes as relevant; include file paths if edited.
4. **Validation:** viewports, interactions, accessibility checks, and build/tests actually run, with outcomes.
5. **Limitations:** inaccessible behavior, missing assets, unverified claims needing owner confirmation, and any remaining deliberate trade-offs.

For an audit-only request, provide actionable findings, a priority order, and exact suggested changes **without pretending edits occurred**. For a reconstruction from a URL or screenshot, identify which details are inferred and clearly state that the result is a separate implementation unless the real source was modified.

**Finish when the screen is coherent, truthful, usable, and distinct—not when every possible decorative technique has been applied.**
