# ASCII Diorama

Create a dense, animated miniature world rendered as coloured monospace glyphs. Adapt the scene to the user's subject while preserving the visual language: a carefully composed 3D diorama, readable text in the focal region, and soft tilt-shift blur toward the foreground and background.

Deliver a working browser scene that animates continuously.

## Inputs and defaults

Use the user's request to determine:

- **Subject and setting:** the place, activity, world, or idea to depict.
- **Focal element:** the feature the viewer should notice first.
- **Mood and palette:** time of day, weather, colour relationships, and atmosphere.
- **Motion:** environmental movement and any moving subjects.
- **Format and constraints:** aspect ratio, controls, runtime, asset policy, performance target, and delivery format.

Respect explicit requirements. When details are missing, choose coherent defaults and proceed. Ask a question only when the missing answer would materially change the result.

If no subject is supplied, invent one with a clear focal element, spatial depth, varied materials, and opportunities for subtle motion. Do not default to a fixed location, culture, biome, building type, or creature.

## Visual direction

### Camera and scale

- Frame the world as a miniature observed from an elevated viewpoint.
- Prefer restrained perspective or an orthographic camera. Avoid wide-angle distortion unless requested.
- Establish foreground, focal region, and background through overlap, scale, lighting, and depth.
- Keep the focal element readable throughout the camera motion.
- Use a fixed camera or a slow, eased movement. Avoid cuts and abrupt turns.
- Choose camera angle, distance, and framing for the subject; numerical settings depend on world scale.

### Composition and colour

- Organize the view around one clear focal element and a readable visual route: a path, structural axis, current, light trail, repeated forms, or another subject-appropriate guide.
- Use contrast in colour, value, shape, or movement to establish hierarchy. A saturated accent is useful when it suits the scene, but is not mandatory.
- Give the foreground and background enough colour and shape variation to remain attractive when blurred.
- Balance detail with open areas so the world reads at glyph resolution.
- Match the palette to the setting rather than imposing greens, golds, or any fixed accent colour.

### Detail and density

- Build richness through many small, varied elements appropriate to the subject: vegetation, windows, machinery, books, stones, cables, furnishings, particles, or repeated architectural details.
- Cluster detail with controlled variation. Avoid uniform scattering and identical spacing everywhere.
- Include gaps, shadows, and changes in height so dense regions retain structure.
- Use instancing, merged geometry, procedural detail, or other suitable techniques to keep the scene efficient.
- Judge density in the final glyph image. Raw polygon and instance counts are not quality targets.

### Life and motion

- Include a few coordinated layers of motion: ambient movement, local activity, and optional camera drift.
- Choose motion that belongs to the setting, such as wind, traffic, flowing liquid, machinery, drifting particles, blinking indicators, or moving figures.
- Give moving subjects believable contact, pacing, and direction. Walking figures should step rather than slide.
- Keep movement subtle enough that the glyph image remains readable.
- Animate deterministically where practical. Separate simulation time from wall-clock time so pause and resume work correctly.

## Glyph rendering

Use actual monospace characters as the visible image structure. Do not substitute a pixelation filter for glyph rendering.

Each cell should derive its:

- **Colour** from the rendered scene over that cell's footprint.
- **Character** from luminance, material or surface identity, and optionally edge direction.
- **Background** from a dim version of the scene, preserving colour between strokes without overwhelming the text.

Define a consistent surface vocabulary. These are examples, not required scene contents:

| Surface or feature | Possible glyphs |
|---|---|
| Thin directional forms | `/`, `|`, `\` |
| Fine irregular texture | `.`, `:`, `*`, `+`, `o` |
| Solid or layered surfaces | `-`, `=`, `#` |
| Flowing or rippling surfaces | `~`, `-`, `=` |
| Bright particulate detail | `*`, `%`, `#` |
| Distinct moving subjects | `&`, `@`, `$` |
| Strong edges | `|`, `/`, `-`, `\` |

Choose glyphs supported by the selected font. Include every character referenced by a rendering rule in the atlas or character set.

Preserve temporal stability: avoid rapidly changing random characters, flickering thresholds, and excessively sensitive edge classification. Anchor procedural variation to the world or a stable cell grid where appropriate.

## Focus and finishing

- Keep the focal region crisp enough to read individual glyphs.
- Apply gradual blur outside that region to create a tilt-shift miniature effect.
- Use screen position, scene depth, or a combination to define focus. Place the focus around the subject rather than at a mandatory image coordinate.
- Preserve the focal element during camera movement; a fixed focus band may need adjustment as framing changes.
- Tune tone mapping, saturation, glyph brightness, and cell background together. Retain shadow detail and prevent bright surfaces from becoming featureless blocks.
- Maintain consistent glyph proportions across viewport sizes. Use the font's measured character aspect ratio rather than assuming square cells.

## Implementation

Choose a browser-compatible approach that fits the requested environment. Prefer existing project conventions when working inside a project. No particular engine, framework, starter scene, downloaded model, or external texture pack is required.

A useful rendering pipeline is:

1. Render the world to an offscreen colour target, with material IDs or other classification data if needed.
2. Sample colour over each glyph cell and select a character using stable surface, brightness, and edge rules.
3. Draw the coloured glyph grid with its subdued background.
4. Apply the focus treatment and final colour adjustments.

Equivalent approaches are acceptable if they preserve the intended appearance and performance.

If storing classification data alongside colour, keep it independent of lighting, transparency, filtering, and tone mapping. Read discrete IDs without interpolating or averaging them. Use a separate data buffer when alpha is needed for transparency.

Use procedural geometry and materials when they are sufficient. Add external assets only when they materially improve the result and are permitted by the task. Keep required assets local to the deliverable where possible, record relevant attribution, and handle loading failures visibly. Do not execute downloaded asset files.

Use floating-point targets, mipmaps, shadows, and post-processing only where supported and beneficial. Provide a practical fallback or explain an observed compatibility limit.

## Workflow

1. **Translate the request into a scene plan.** Choose the subject, focal element, visual route, palette, depth layers, and motion. Keep the plan brief and proceed to implementation.
2. **Block out the composition.** Establish camera, major forms, lighting, and framing before adding dense detail. Check the result at the intended glyph resolution.
3. **Build the world.** Add subject-appropriate materials, clustered details, and activity. Avoid carrying unrelated props or assumptions over from a reference scene.
4. **Implement the glyph treatment.** Make colour sampling, character selection, cell proportions, and surface classification work together.
5. **Tune focus and motion.** Keep the main subject legible, stabilize glyph changes, and smooth camera turns and animation transitions.
6. **Inspect the actual browser output.** Check screenshots and live motion, along with console errors and asset loading. A successful build alone does not verify the visual result.
7. **Package the result.** Include the source or static build, required assets, dependency information, and concise run instructions appropriate to the requested format.

## Validation

Before delivery, verify:

- The requested subject is recognizable at the final display size.
- The focal element is clear and stays within the intended focus region.
- Sharp regions visibly consist of glyphs, with enough tonal variation to describe forms.
- Foreground and background blur add depth without obscuring essential content.
- Motion is coherent and glyph changes do not strobe.
- Pause and resume work if controls are included.
- Resizing preserves framing, glyph proportions, and usable layout.
- Required assets load and the console has no unresolved rendering errors.
- Performance is acceptable in the tested browser and viewport.
- The packaged deliverable runs using the supplied instructions.

Reduce detail, shadow cost, render resolution, or post-processing samples when needed. Preserve the focal hierarchy and glyph readability while tuning performance.

## Continuous animation versus seamless loops

Continuous playback does not automatically produce a seamless finite loop. For an interactive scene, use smooth ongoing motion and hide resets outside the visible region.

If a seamless loop is requested, choose a common duration, synchronize periodic motion, and ensure camera, particles, subjects, procedural effects, and any animation clips return to matching states. Inspect the boundary explicitly. Do not claim a seamless loop based only on continuous playback.

## Delivery

Return the complete requested artifact. For a browser scene, provide the runnable files or build and the command or entry point needed to view it. Briefly describe controls and any observed limitations.

Do not claim visual inspection, performance measurements, compatibility, or seamless looping unless verified. If verification is unavailable, say what remains untested.
