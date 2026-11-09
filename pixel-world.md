---
name: ambient-pixel-worlds
description: Create large, continuous, atmospheric, procedural pixel-art 3D worlds and cinematic ambient scenes. Includes a recoverable offline WebGL2 runtime, world streaming, procedural component primitives, weather, time-of-day, audio, camera controls, capture, and an extensive custom-component development discipline. Use for requests involving ambient worlds, explorable landscapes, stylized settlements, dioramas, cinematic flythroughs, procedural scenes, and new world objects that do not yet exist.
---

# AMBIENT PIXEL WORLDS — SELF-CONTAINED SKILL

**Version:** 1.0 (standalone foundation)  
**Artifact form:** exactly one Markdown file  
**Runtime:** dependency-free, single-file HTML/WebGL2 application embedded as a compressed payload at the end of this SKILL  
**Reference inspiration:** [Noxe Pixel Engine](https://github.com/danieljan-design/noxe-pixel-engine) by Daniel Jankulovski (MIT). This package is an independently written, smaller portable implementation based on the same broad procedural/pixel-rendering ideas; it is **not** an exact copy of Noxe's source and does **not** claim feature-for-feature parity.

## 0 — Operational mandate

You are the engineer, environment artist, procedural technical artist, cinematographer, and QA reviewer. Convert a user's natural-language world idea into a **working, artistically coherent, runnable world**, using the included runtime as a starting point. **Never reject a request merely because a particular building, creature, vegetation species, structure, vehicle, prop, phenomenon, or effect is absent from the default library.** Create a new component to the required standard. A missing component is an implementation task, not an excuse to substitute a generic cube or omit the feature.

Five governing rules:

1. **Ship working artifacts, not imaginary assets.** Create/modify runnable HTML/JS and test what you reasonably can. Do not describe a feature as implemented if it remains a placeholder.
2. **Architecture before ornament.** Establish world scale, topology, composition, geography, sightlines, functional relationships, and animation before fine decoration.
3. **Strong silhouettes and legibility at native low resolution.** The viewer must recognize the object at the actual internal pixel resolution, not only at a highly zoomed inspection image.
4. **Determinism and bounded cost.** Use supplied seeds and world-coordinate sampling; respect LOD, chunk memory, draw calls, update cost, and replay reproducibility.
5. **Maintain extensibility.** Reusable components belong in named builders with explicit interfaces. Scenes describe arrangements and behaviors; they should not become enormous collections of unrelated hardcoded mesh operations.

If the user's intent is ambiguous, choose a coherent initial composition rather than stopping. Preserve any specific references, era, biome, lighting mood, architectural style, time, weather, movement, and output format the user provides. Do not assume “ambient” necessarily means idle: slow, purposeful life can be essential (windows, weather, vehicles, water, pedestrians, wildlife).

### What is actually included

**Implemented in the embedded runtime:** independent WebGL2 toon/pixel renderer; low-resolution framebuffer; nearest-neighbor upscale; simple depth-based outlines; tonal quantization and Bayer dithering; time-based lighting/sky; approximate fog; rain and snow particles; simple water and wet/snow surface effects; deterministic world height field and biomes; camera-centered chunk streaming; near/mid/far geometry density; runtime resource disposal; pixel-snapped orthographic camera; drag/zoom/WASD/cinematic motion; procedural component registry; basic animated meshes; WebAudio ambient synthesis; PNG capture; and real-time WebM recording, optionally with synthesized sound when sound was enabled.

**Not yet equivalent to the original Noxe:** HDR bloom, deferred normal-aware outlines, full planar reflections, true cast shadows/shadow maps, physically spatialized sound sources, high-fidelity water reflections, H.264/AAC MP4 muxing, deterministic offline video export, collision/navigation, indoor/outdoor acoustic propagation, arbitrary large-scale physics, or sophisticated character rigs. Implement any of these explicitly when the user's project needs them. Never imply this runtime inherits them automatically from the Noxe repository.

**Self-contained** means its HTML has no asset downloads, imports, libraries, or build step. A WebGL2-capable browser is still required. Real-time recording depends on browser MediaRecorder support and does not substitute for deterministic offline encoding.

## 1 — Extract the included runtime

The `SKILL.md` instructions and the *complete general code of this standalone runtime* are packed into this one file. The long HTML is gzip-compressed and Base64-encoded to keep the skill readable and transportable; the payload is an exact source archive, **not** a URL or retrieval instruction. **You must extract it rather than reconstructing the renderer from memory.**

Place this file on disk as `SKILL.md`. Run from the directory containing it:

```bash
python3 - <<'PY'
from pathlib import Path
import base64, gzip, hashlib

source = Path('SKILL.md').read_text(encoding='utf-8')
start = '\n<!-- BEGIN_AMBIENT_RUNTIME_GZIP_BASE64 -->\n'
end = '\n<!-- END_AMBIENT_RUNTIME_GZIP_BASE64 -->\n'
assert source.count(start) == 1 and source.count(end) == 1, 'Missing or duplicate runtime payload markers'
encoded = source.split(start, 1)[1].split(end, 1)[0].strip()
html = gzip.decompress(base64.b64decode(''.join(encoded.split()), validate=True))
assert html.startswith(b'<!doctype html>'), 'Invalid extracted HTML'
assert hashlib.sha256(html).hexdigest() == '0ff712ee6ba0324ad886b246e915974f2366262cb5326d41262e26b3ccf3a7f6', 'Runtime hash mismatch'
Path('world.html').write_bytes(html)
print('Created world.html | bytes:', len(html), '| SHA256:', hashlib.sha256(html).hexdigest())
PY
```

Open `world.html` in a browser; if local-file security rules interfere with browser APIs, use the Python standard-library static server:

```bash
python3 -m http.server 5184
```

Then open `http://localhost:5184/world.html`. No `npm install` or remote network access is needed.

**Do not edit the Base64 payload by hand.** Extract the HTML, edit it with ordinary tools, verify it, and ship the modified `world.html` to the user. If packaging a revised single Markdown skill, recompress the tested HTML, replace the payload, and update version/feature notes. This rule prevents describing code that is different from the embedded code.

### Existing runtime entry points

The global `window.AmbientWorld` is available after initialization:

| API | Use |
|---|---|
| `registerComponent(id, spec)` | Define a reusable procedural component; `spec.build(ctx)` is required |
| `components` | Component registry (`Map`) |
| `world` | Current config, camera, state, chunk cache, metrics |
| `heightAt(x,z)` | Deterministic world-space terrain elevation |
| `biomeAt(x,z,h?)` | Classify world location |
| `waterHere(x,z)` | Is surface water present? |
| `random(seed)` | Seeded RNG factory |
| `hash(x,z,seed)`, `noise(x,z,seed)`, `fbm(x,z,seed)` | Deterministic scalar fields |
| `rgb(hex)`, `tint(hex,factor)` | Palette helpers |
| `Builder`, `ScopedGeometry` | Procedural geometry APIs |
| `setPreset(name)` | Change built-in biome/settlement preset |
| `refreshWorld()` | Dispose/regenerate chunk cache after geometry/placement changes |
| `render()`, `downloadPNG()` | Render or capture |
| `environment()`, `updateChunks()` | Current weather/lighting; streaming update |

No guaranteed public API exists beyond what is implemented or explicitly added. Never hallucinate methods such as `.loadGLTF()`, `.addPhysics()`, `.loadTexture()`, or `.exportMP4()`.

### Project declaration

Insert a project declaration **before** the main inline runtime `<script>` in the extracted HTML. It is read at initialization:

```html
<script>
window.AMBIENT_PROJECT = {
  preset: 'highlands',
  world: {
    seed: 28317,
    pixelSize: 3,
    chunkSize: 24,
    nearRadius: 3,
    farRadius: 5,
    maxChunksPerFrame: 2,
    quantization: 13,
    pixelSnapping: true
  },
  camera: { x: 12, z: 8, y: 2.5, yaw: 0.8, elevation: 0.58, size: 32, drift: 0.35 },
  hour: 19.2,
  weather: 'rain',
  placements: [
    { type: 'cabin', x: 12, z: 11, scale: 1.2, roof: '#665b70', maxLod: 2 },
    { type: 'lamp-post', x: 17, z: 8, maxLod: 1 }
  ]
};
</script>
```

This *changes/augments* the starting configuration; it does not turn the engine into a declarative scene graph. For complex roads, rivers, terrain rules, reactive lighting, architectural districts, or dynamic populations, modify the world-generation and component modules deliberately as described below.

## 2 — Understand the world model

**Axes:** +Y is up, X/Z form the ground plane. A component origin is normally at ground contact and has a local up direction of +Y. Camera `yaw` and `elevation` are in **radians**; config `size` is vertical **world units visible**. World coordinates are independent of the floating-origin GPU coordinates.

**Terrain:** `heightAt(x,z)` must be pure and deterministic: same `(x,z,seed,worldParameters)` → same elevation regardless of which chunk requests it. World-space terrain must not depend on chunk RNG calls, generation order, or frame time.

**Chunks:** chunk coordinate `(cx,cz)` covers `[cx*S,(cx+1)*S)` × `[cz*S,(cz+1)*S)`, where `S=chunkSize`. Geometry is authored in world coordinates and uploaded relative to the chunk center. Chunks cache a static mesh, zero or more dynamic meshes, and optional updaters. Each chunk must dispose its GPU buffers before eviction.

**LOD:** runtime `lod=0` is nearby (highest detail), `lod=1` mid-distance, `lod=2` distant (fewest vertices). Near and far meshes are independently generated from the same sampled world. The default terrain uses larger sample spacing at higher LOD; real production systems should add edge stitching/skirting and cross-fade or geomorph transitions where needed to eliminate visible T-junctions and pop-in.

**Camera origin shift:** the renderer recenters every 2048 units to avoid float precision degradation. Always use the world-space `x,z` coordinates for procedural sampling and placement. Use relative coordinates only at GPU draw time.

**Time:** `world.time` is seconds since startup and `world.hour` is time of day. Effects that must replay identically should derive state from `world.time` and stable seeds, not accumulate unbounded `Math.random()` state.

**Ambient sound:** audio requires user interaction to unlock in most browsers. The default implementation has nonspatial wind, rain, water, and crickets. Do not claim binaural/spatial propagation unless you implement positional nodes and validate distance falloff.

## 3 — World creation workflow (mandatory)

### Phase 1. World brief

Before building, construct an internal brief:

- **Biome, climate, geography:** e.g. fjord, humid rainforest, rainy Japanese backstreet, alpine basin.
- **World scale and camera:** one vista, walkable district, extended flythrough, or streaming limitless terrain.
- **Primary focal structures:** one major landmark, 2–4 secondary focal anchors, supporting structures.
- **Mood:** palette, season, hour, weather, wind intensity, fog, primary/emissive light relationship.
- **Life and motion:** ambient cycles, water, animals, vehicles, vegetation, weather, people.
- **Output and constraints:** live browser, screenshot, recorded WebM, offline MP4 (requires extra implementation), target resolution/frame time.
- **Explicit exclusions:** items/style combinations the user does not want.

Translate broad natural-language intent into tangible visual behaviors, not a shallow theme label. “Atmospheric” must have an identified cause: low-angle warm lighting, layer-separated fog, spatial variation, intermittent motion, depth silhouettes, sound response, etc.

### Phase 2. Spatial design, before meshes

Create a rough world plan: elevation zones; water network; traversable paths; location of settlement; bridges/harbor access; vegetation density; landmark views; camera paths; deep background; transition areas. Every road should connect to somewhere; buildings must stand on plausible ground, docks must meet water, bridges must span an obstacle, and wildlife should occupy suitable habitat.

Treat **hydrology as connected geography**. A river should follow a continuously descending elevation field or an intentional lake basin; not appear as random blue rectangles. The simplified starter runtime uses water-covered terrain cells, so more complex shorelines demand a coherent water/terrain interface extension.

For cinematic compositions, plan three depth strata: detailed foreground silhouettes; story-bearing midground; simplified, atmosphere-receding background. Never populate every square meter uniformly: use open space and rhythm around the focal area.

### Phase 3. World functions

Write pure, shared world-coordinate field functions for terrain, moisture, temperature, biomes, flow direction, path distance, and land-use zones. Build the first major features and confirm the terrain is coherent before placing hundreds of props. Avoid arbitrary city grid stamping on steep slopes.

Recommended procedural hierarchy:

```text
world seed + global geography parameters
 ├── terrain field and hydrology
 ├── climate/moisture/slope/temperature fields
 ├── biome/land-use classification
 ├── settlement, roads, paths, bridges and infrastructure
 ├── landmarks and named areas
 ├── large vegetation/rock formations
 ├── medium structures, props, and vegetation
 ├── small scene dressing (near LOD only)
 ├── ambient motion and event schedules
 └── camera / sound / weather presentation
```

Generate coarse global structure first, then local detail. Use grid or graph constraints for roads and town blocks. Use Poisson-disk or blue-noise-inspired distributions for natural items; reject overlaps and incompatible terrain. For each position, evaluate slope, elevation, waterline, visibility, local ecology, and clearance before placing an object.

### Phase 4. Build, review, refine

Render an early version with just geography, water and primary landmarks. Evaluate composition from the requested camera. Only then add houses, trees, props, sound and animation. Review dawn/day/dusk/night; clear/rain/snow/fog; near and wide camera; and several camera positions across chunk boundaries. Remove objects that are technically present but unreadable, mis-scaled, floating, or distract from the focal story.

### Phase 5. Delivery

Provide a working HTML build, instructions to open it, implemented controls, any known limitations, a screenshot if verified, and an accurate capability statement. If asked for source as a single `.md`, include the runtime package directly in that Markdown, as this skill does.

## 4 — Critical pixel-world art direction

The scene is 3D, but its final pixels are an intentional graphic vocabulary. Design at **render resolution**, not solely at mesh resolution. For canvas output height `H` and pixel size `p`, the low-resolution render height is approximately `H_low = ceil(H/p)`. For an orthographic camera with visible vertical extent `S`, approximately:

`art pixels per world unit = H_low / S`  
`minimum thickness in world units for k pixels = k * S / H_low`.

Example: output 1080 px high, pixel size 3, `H_low=360`; camera size 30 yields roughly 12 art pixels per world unit. A 2-art-pixel window frame needs about 0.167 world units. Do the calculation before authoring microscopic geometry that disappears or flickers.

**Silhouettes:** use distinctive rooflines, tree crowns, vehicle bodies, natural breaks, and proportional landmarks. A building must be recognizable as a building at native scale. A pine needs a trunk–crown–tier proportion, not just a green spike. A lighthouse needs a tower, gallery, lantern housing and context, not only a striped cylinder.

**Value hierarchy:** reserve the brightest values for important light sources, snow highlights, sunlit faces, and focal elements; keep distant geometry more compressed and color-coherent. Night lighting must be localized and motivated by lamps, windows, fires or neon. Avoid uniform overglow.

**Palette discipline:** build a constrained family of warm, cool, neutral and accent colors. A biome should share material language while showing controlled variation. Do not assign independent random colors per triangle. Use stable per-object and per-region variation so the world looks art-directed, not noisy.

**Edge logic:** outlines should emphasize meaningful depth differences. Thin, redundant geometry can create too many dark seams and undermine the visual style. At far LOD remove detail instead of drawing smaller versions of it.

**Material causality:** wet roads darken; puddles cluster in depressions; snow accumulates on upward/exposed faces; sheltered locations have less precipitation; emissive windows illuminate visually only at the right hours. The starter runtime approximates some effects globally—extend it for physically meaningful local behavior when the scene demands it.

**Motion:** choose slower, staggered, loop-compatible cycles for ambient worlds. Wind should affect vegetation by type/height; rain movement must be vertical with wind shear; a vehicle follows roads, not straight across terrain; water motion should convey flow direction; light flicker should be rare and restrained.

**Composition:** use intentional occlusion and negative space. Don't fill the entire view with tall trees. Use a legible horizon, strong focal contrast, and a few landmarks to establish scale. Test panoramic, portrait, and zoomed frames separately.

## 5 — CUSTOM COMPONENT CREATION: FULL PROCEDURE

This section is mandatory when anything requested is missing or insufficient in the built-in library. The word **component** includes geometry, materials, behavior, sound, interaction, placement logic and environmental response—not just a mesh.

### Step A — Define the component contract before writing code

Create a short component specification with:

| Field | Required decision |
|---|---|
| ID/category | Stable kebab-case identifier, e.g. `clock-tower`, `sakura-tree`, `canal-boat`, `firefly-swarm` |
| Visual identity | The 3–7 cues by which a viewer recognizes it at native pixel scale |
| Real-world or stylized scale | Width × height × depth in world units, relative to nearby people, doors, trees, roads |
| Anchor and orientation | Ground-contact origin; which side is front; rotation axis and pivot locations |
| Geometry inventory | Major masses, secondary shapes, tertiary details |
| Material palette | Body, trim, roof/skin/leaf, metal, glass, emission, weather states |
| Variability | Allowed seeded changes in proportion, color, wear, age and layout |
| LOD policy | Exactly what appears at LOD 0, 1, 2 and which tiny features disappear |
| Motion | Dynamic children; loop duration, phase, event triggers and safe bounds |
| Interaction | Trigger areas, optional click/action state, feedback if applicable |
| Environmental response | Sun/night, rain, snow, wetness, wind, fog, terrain and waterline |
| Placement | Terrain slope, moisture, proximity to roads/water, orientation, spacing/clearance |
| Performance | Vertex/triangle count, dynamic draw calls, number of instances, update complexity |
| Tests | Minimum render views and states, regression cases, semantic acceptance criteria |

The specification prevents a common failure: code that technically draws a recognizable noun but misses the details that make the noun convincing in the requested environment.

### Step B — Search the existing library and compose instead of duplicating

Inspect `registerComponent(...)` calls and the available `ScopedGeometry` primitives. Existing types: `pine`, `deciduous`, `rock`, `grass-tuft`, `cabin`, `lamp-post`, `dock`, `windmill`, `boulder-arch`. Reuse their design patterns and geometry helpers, but do not pass undocumented parameters or assume hidden assets. A custom `fishing-village-house` might reuse the cabin body pattern while adding a foundation on stilts, outdoor stairs, hanging nets and local material choices. A bridge may need an entirely new structural builder.

If the requested shape requires a new primitive—arched vault, spline-following road, swept pipe, lathed tower, pitched roof with dormers, rock outcrop, cloth sail, irregular canopy—**implement that primitive** and expose it deliberately in a reusable API. Don't replace a signature feature with unrelated basic primitives simply to avoid engineering work.

### Step C — Build a proportionally correct geometric hierarchy

1. Block out the **largest masses** first using boxes, cylinders, cones and polygons. Confirm footprint, anchor, height and readable silhouette.
2. Establish **real construction logic**: roof supported by walls, beams attached to posts, columns under a bridge deck, stairs rising to an entrance, ship masts attached to a hull, branches supporting foliage.
3. Define material boundaries, paneling and repeated structural features with consistent spacing; windows/doors must fit inside walls.
4. Add small decorative elements only when they occupy enough art pixels to matter.
5. Add strong asymmetric cues where they help recognition: chimney, porch, signage, antenna, lantern room, hanging baskets, tracks or ropes.
6. Test whether the object's major features are readable against likely background values, particularly dusk and fog.

**Geometry requirements:** every coordinate must be finite; dimensions must be positive; triangles must be nondegenerate; the pivot must be where rotations physically occur; no accidental z-fighting; no open cracks at joined masses; no unsupported floating attachments. For terrain-contacting objects, evaluate `heightAt` at all footprint corners, not only the center. On uneven ground, add a foundation/terrace or reorient suitable small props to the local normal. Avoid steep-slope placement unless explicitly designed for it.

### Step D — Use the actual procedural component API

Inside the extracted HTML, `registerComponent` accepts `{ category, build(ctx) }`. `build(ctx)` receives:

| Context member | Semantics |
|---|---|
| `ctx.type` | Registered component ID |
| `ctx.geo` | `ScopedGeometry`, anchored at the chosen placement, scaled and yaw-rotated |
| `ctx.rng()` | Stable per-instance seeded uniform random number, `[0,1)` |
| `ctx.seed` | Stable integer seed for this instance |
| `ctx.lod` | 0 near, 1 mid, 2 far |
| `ctx.x`, `ctx.y`, `ctx.z` | Absolute world coordinates; Y defaults to `heightAt(x,z)` |
| `ctx.params` | Frozen copy of placement parameters including `scale`, `rotation`, custom fields |
| `ctx.world.heightAt(x,z)` | World elevation query |
| `ctx.world.biomeAt(x,z)` | Biome classification |
| `ctx.world.waterHere(x,z)` | Water query |
| `ctx.world.mode`, `ctx.world.waterLevel` | Broad world mode and water elevation |
| `ctx.dynamic(buildFn,updateFn)` | Create independent dynamic GPU mesh with per-frame transform callback |
| `ctx.place(type,dx,dz,options)` | Place a nested component at a world-space offset (guard against recursion) |

`ctx.geo` implements:

```js
geo.box(x, y, z, width, height, depth, color, emissive = 0)
geo.cone(x, y, z, radius, height, color, radialSides = 6)
geo.cylinder(x, y, z, radius, height, color, radialSides = 8)
geo.roof(x, y, z, width, depth, rise, color)
geo.beam([ax, ay, az], [bx, by, bz], thickness, color)
geo.triangle([x,y,z], [x,y,z], [x,y,z], color, emissive = 0)
geo.quad([x,y,z], [x,y,z], [x,y,z], [x,y,z], color, emissive = 0)
geo.p(x,y,z)  // convert local coordinates to world coordinates
```

**Important:** primitive `y` values are **local offsets from the ground-contact anchor**, not absolute terrain elevations. `box(..., y, ..., h, ...)` uses **bottom Y**, not center Y. Distances and dimensions are multiplied by placement `scale`. `rotation` is yaw in radians. Emit a *single stable piece* into `ctx.geo` whenever possible: it is batched into the chunk's static GPU mesh. Only moving subparts should be dynamic. For best lighting, author polygons with a consistent winding order; the standalone renderer disables culling but triangle normals still control shading.

`emissive` is an intentionally simplified brightness contribution; use small, controlled positive values for windows and lamps. Do not use an emissive flag as a substitute for an actual spatial light source when the scene requires cast illumination.

### Step E — Example: detailed custom lighthouse, including a rotating beacon

This is **real component code against the included API**. Insert it inside the runtime `<script>` at the `// AMBIENT_COMPONENT_INSERT` marker, not after `</script>`. It needs no models, image files or imports.

```js
registerComponent('coastal-lighthouse', {
  category: 'architecture',
  build(ctx) {
    const { geo: g, rng, lod, params } = ctx;
    const towerH = params.towerHeight ?? 7.2;
    const r = params.radius ?? 0.95;
    const stone = params.stone ?? '#e7e1cf';
    const trim = params.trim ?? '#b44e4b';
    const metal = '#495260';

    // Footprint, structural tower, cornice and lantern gallery.
    g.cylinder(0, 0, 0, r * 1.22, 0.38, '#898b8d', 10);
    g.cylinder(0, 0.32, 0, r, towerH, stone, 10);
    for (const y of [1.55, 3.45, 5.35]) {
      g.cylinder(0, y, 0, r * 1.015, 0.30, trim, 10);
    }
    g.cylinder(0, towerH + 0.10, 0, r * 1.18, 0.23, metal, 10);
    g.cylinder(0, towerH + 0.32, 0, r * 0.72, 0.92,
               '#f2dcaa', 10); // glass lantern chamber silhouette
    g.cone(0, towerH + 1.20, 0, r * 1.02, 0.78, metal, 10);

    // Tall focal door and stepped walkway. Skip undersized far details.
    g.box(0, 0.39, -r - 0.045, 0.64, 1.45, 0.055, '#4c4c50');
    g.box(0, 0.0, -r - 0.72, 1.05, 0.18, 0.95, '#918a7f');
    if (lod === 0) {
      for (const y of [2.45, 4.55]) {
        g.box(0, y, -r - 0.055, 0.36, 0.58, 0.05, '#343947');
        g.box(0, y + 0.07, -r - 0.072, 0.23, 0.39, 0.05,
              '#ffe4a7', 1.15);
      }
      // Support rails: big enough to register as a designed silhouette.
      for (let i = 0; i < 8; i++) {
        const a = 2 * Math.PI * i / 8;
        g.box(Math.cos(a) * r * 1.06, towerH + 0.2,
              Math.sin(a) * r * 1.06, 0.095, 0.58, 0.095, metal);
      }
    }

    // Separate dynamic mesh: pivot is at its own local origin.
    if (lod < 2) {
      const beacon = ctx.dynamic((blade) => {
        blade.box(0, 0, -0.63, 0.40, 0.20, 1.28,
                  '#ffd799', 1.7);
        blade.box(0, -0.08, 0, 0.37, 0.37, 0.37,
                  '#fff4c7', 2.3);
      }, (part, t) => {
        part.x = ctx.x;
        part.y = ctx.y + (towerH + 0.82) * (params.scale ?? 1);
        part.z = ctx.z;
        part.angles[1] = t * 0.55 + ctx.seed * 0.0001;
      });
    }
  }
});
```

To place it, add the following object to `window.AMBIENT_PROJECT.placements`:

```js
{ type: 'coastal-lighthouse', x: 18, z: -26,
  scale: 1.15, towerHeight: 7.8, radius: 1.0,
  stone: '#e7e1cf', trim: '#bb514a', maxLod: 2 }
```

The beacon above is **a rotating luminous 3D form**, not a volume-rendered spotlight or a reflection-casting light beam. To meet a prompt requiring a sweeping beam in fog or light projected onto terrain, extend the renderer with an appropriate translucent cone/beam shader or dynamic spotlight implementation, then test it in rain and fog. **Do not call a crude emissive bar a physically simulated beacon.**

### Step F — Make every component reusable, not a one-off statue

A strong component is configurable but not undisciplined. Prefer parameters that have clear semantic meaning: `height`, `roofPitch`, `windowRows`, `weathering`, `woodTone`, `leafDensity`, `age`, `lit`, `rotation`, `variant`. Keep scales reasonable and clamp them. Do not let every parameter drive every detail randomly. Support seeded variants so repeated houses share a family resemblance without cloning perfectly.

Use **component families** for related forms: e.g. `canal-house`, `canal-warehouse`, `canal-pier` can share a construction kit with common brickwork and roof rules. Reuse geometry logic with helper functions rather than copy/paste. Any new helper added to the runtime must have an explicit signature and example invocation.

Provide placement defaults, a sensible fallback palette, stable seed behavior, and a far-LOD silhouette. When a requested component has a special state (doors open, neon flashing, sail reefed, tree flowering), expose the state intentionally instead of burying it in frame-specific randomization.

### Step G — Dynamic and animated components

Use `ctx.dynamic((geo)=>{...}, (part,time,dt)=>{...})` for pieces that must move independently. The callback can alter `part.x`, `.y`, `.z`, and `.angles = [rotationX, rotationY, rotationZ]` in radians. The object's local mesh is generated about its **own zero origin**; place the pivot at an actual hinge, axle, neck, lantern center or mast connection. Geometry is uploaded once; the callback modifies only transform uniforms. Avoid regenerating or allocating meshes every animation frame.

**Examples:** windmill blades use Z rotation; weather vanes use Y rotation; a hanging sign uses X oscillation; a ferry needs translation and yaw from a stable path parameter; a slowly rotating star observatory uses Y rotation. Seed phase offsets per object, but keep motion deterministic for recordings: `theta(t) = angularSpeed * t + seededPhase`. If state should persist (e.g. a door is opened), store it in a named component state object and ensure a replay can set/reset it.

For hierarchical multi-joint animation the small runtime supports only independent mesh transforms. Implement a parent-child transform graph before claiming complex character rigs. Use bounded easing and no geometry penetration. A walking animal needs properly connected body/neck/legs/tail and deliberate cycles—not the entire animal translating mechanically through the ground.

### Step H — Terrain contact and environmental integration

Custom architecture must match land and engineering constraints. Evaluate heights at building corners; if variation exceeds a planned threshold, either reject the site, terrace it, or provide visible foundation stilts. Orient doors toward an actual road/path. On water, create floating supports or grounded piers and place hulls at the shared water plane, not at terrain elevation. For bridges, define two reachable approaches and an obstacle span first; only then generate posts/deck/railings.

Natural components require placement rules based on biome, moisture, altitude, slope and competition. Different tree species occupy distinct ecological niches; rocks cluster geologically; reeds gather at shorelines; mushrooms prefer damp understory; snow cover follows exposure. Use **stable world-coordinate randomness** so objects don't shift when neighboring chunks load or LOD changes. Large structures must occupy only one authoritative chunk or world landmark record, not be independently generated on both sides of chunk boundaries.

Check bounding volumes for overhang, shadow/reflection extent, and potential collisions. Static objects near edges may extend across chunks; define ownership by origin and ensure the culling bounds account for the full geometry. Rebuild derived nav/placement data when geometry changes.

### Step I — LOD and performance requirements

For each new type, implement a three-tier plan:

| LOD | Required appearance | Typical omitted details |
|---|---|---|
| 0 (near) | Recognizable main and secondary geometry, meaningful trim, windows, controls, animation | Tiny bevels invisible at current camera pixel density |
| 1 (mid) | Full silhouette, one or two defining details, limited motion where important | Most panels, individual flowers, tiny rails, repeated studs |
| 2 (far) | Few large masses with representative palette and distinct silhouette | Small geometry and almost all per-instance animation |

Measure cost in vertices, triangles, draw calls, GPU buffers, shader complexity, and JS CPU. Batch static geometry into the chunk mesh, not one draw call per window or tree. Repeated components with identical geometry should ideally use instancing when refactoring the runtime beyond the initial batching strategy. Share material/state decisions; avoid hidden offscreen or subpixel work. Use short-distance culling for particles and small props. Maintain a deterministic cap on active dynamic components.

**Special warning:** the sample runtime switches chunk mesh resolution at LOD boundaries. For production-quality terrain, implement stitching, skirts, morphing, or fixed edge sampling at adjacent LOD seams. Don't declare the streaming system “seamless” until moving-camera boundary tests confirm it visually.

### Step J — Animation/audio/weather fidelity

Ambient behavior is a system, not a decorative add-on. Design state transitions rather than isolated toggles:

- **Rain:** rain amount controls streaks, wet surfaces, puddles, roof tapping, low visibility, and calmer wildlife behavior.
- **Snow:** snowfall controls particle drift, surface accumulation, light reflection and reduced vegetation contrast.
- **Night:** changes sky, moon/ambient light, windows, signage, insect/animal activity, silhouette separation.
- **Wind:** drives canopy and flags with different motion spectra, affects water and precipitation direction.
- **Water:** reflects shore layout and wind/flow conditions; flowing rivers need directional field and shore collision.
- **Human activity:** windows turn on at varied times, moving vehicles obey paths/signals, lights vary according to neighborhood function.

The included sound engine is an ambient bed. To make a new *spatial* sound component, extend `AudioEngine` with one owned `PannerNode` per audible emitter (or a pooled voice), a stable oscillator/noise/audio source, and a distance gain model; update emitter and listener positions from world coordinates and the camera; suspend muted distant emitters; route audio to both the speakers and `streamDest` for recording; dispose nodes on chunk eviction. Handle mono/ambisonic assumptions explicitly and test on headphones and speakers. Never create an oscillator every frame.

### Step K — Custom shaders, effects, and unsupported primitives

When the supplied renderer lacks something essential (volumetric lighthouse beam, water caustics, proper wet reflections, ribbons, foliage sway, spritesheet animation, soft shadows, multi-bounce light, etc.):

1. Describe the visual/mechanical requirement precisely and identify the pass in which it belongs: geometry, main material, environmental particle system, post-processing, or audio.
2. Decide whether a geometry solution is sufficient or a shader is genuinely justified.
3. Add a **named** GLSL shader and only the required uniform/attribute interfaces. Keep WebGL2 / GLSL ES 3.00 compatibility and explicit precision qualifiers.
4. Reuse the existing program/shader helpers; update compile/link checks, uniforms, initialization, viewport/depth/blend states, and resource disposal.
5. For animated effects, derive time from `world.time` and position noise from **absolute** world coordinates or stable IDs. Avoid shimmer induced by camera movement or floating-origin changes.
6. Define how the effect participates in depth ordering, fog, weather, outlines, quantization, and recording. Transparent effects should be rendered with deliberate depth/blend state and correct draw order.
7. Provide graceful low-cost fallback for weaker GPUs or far LOD, without silently removing the core requested effect.
8. Test shader compilation, mobile precision, edge pixels, weather/night, resize, context loss, and restore of GL state.

The starter runtime's simple emissive colors cannot replace a proper light-casting/shadowing system. If a scene explicitly depends on shadows cast by a rotating beacon, an illuminating flashlight, or mirror reflections in rain, implement the necessary rendering pass instead of claiming the approximations suffice.

### Step L — Mandatory component quality gate

Review every new component with all of the following gates. A serious failure in any gate is a blocker:

1. **Recognition:** identifiable in a 3-second glance at native rendered pixel scale.
2. **Proportion:** matches nearby objects and its own construction requirements.
3. **Structural integrity:** rests plausibly on terrain/water; parts attach correctly; no unwanted gaps or clipping.
4. **Art direction:** coherent palette, value structure, silhouette, highlight placement, and weather behavior.
5. **Variation:** seeded, bounded, believable, and stable across reload/streaming.
6. **Animation:** pivot correct, smooth, loopable if applicable, deterministic, no unintended mesh intersection.
7. **LOD:** remains identifiable when simplified; no sudden popping of defining features or visible cracks.
8. **Camera:** works near/far, four azimuths, low/high elevation, still and traveling camera.
9. **Environment:** dawn/day/dusk/night and clear/rain/snow/fog checked; emissive cues readable, not blown out.
10. **Performance:** under agreed frame/draw budgets; no per-frame mesh allocation or leaked GPU resources.
11. **Integration:** doesn't overlap roads/doors, float over terrain, duplicate across chunks, or contradict world topology.
12. **Maintainability:** named, parameterized, documented, minimal assumptions, true API use, no duplicate helpers.

If a component fails, revise it before multiplying it across the world. One convincing structure contributes more to visual quality than a thousand low-quality repetitions.

## 6 — Custom component validation examples

Use these as concrete validations, adapted to the actual user brief.

**Custom wooden fishing pier:** validate that posts reach beneath the water and into terrain, decking runs across structural beams, railings appear where appropriate, scale is suitable for humans and boats, materials are weathered but coherent, and the walkway leads to land. Wind/waves can move attached boats; the pier itself does not bob.

**Custom train/tram:** validate carriage silhouette, bogies/wheels, meaningful front/rear, headlamp/taillamp separation, track alignment, consistent gauge, path curvature, motion speed, stopping behavior and sound tied to movement. Do not place an unrelated rectangular capsule over a road and call it a train.

**Custom flowering tree:** validate asymmetric trunk, branch support, clustered canopy rather than a single sphere, season-dependent blossoms, restrained falling petals near the camera, adequate outline contrast against the sky, and species-appropriate biome placement.

**Custom mountain bridge:** validate two terrain connection points, elevation at both ends, supporting structure and spans, readable deck width, rail height, visibility from main camera, plausible access roads and LOD silhouette.

**Custom fire/lantern:** validate anchored fuel or wick, heat-color hierarchy (bright center to darker outer flame), flicker frequency, night-only secondary glow as appropriate, wind response, smoke trajectory, and a real light source if illumination on nearby surfaces is specifically required.

## 7 — Testing and review protocol

### Automated static checks

After extracting `world.html`, validate the JavaScript syntax independently of a browser:

```bash
python3 - <<'PY'
from pathlib import Path
s = Path('world.html').read_text()
assert s.count('<script>') >= 1
js = s.split('<script>', 1)[1].split('</script>', 1)[0]
Path('runtime-check.js').write_text(js)
PY
node --check runtime-check.js
```

**Note:** if you inserted a project `<script>` **before** the runtime, the simple extraction above will check only the first script. For an augmented world, validate each inline script separately or use a robust HTML parser and run the file in a browser. `node --check` verifies syntax only; it cannot validate shaders or rendering.

### Browser checks

Use recent Chrome/Edge/Firefox with GPU-enabled WebGL2. Check:

- Loads with no console errors; no shader compilation/link errors.
- `window.AmbientWorld.world.chunks.size` grows and stabilizes; no per-frame accumulation in a stationary world.
- Render visible at normal resolution and correctly scales when window resizes.
- Drag-to-rotate, shift/right-drag-to-pan, wheel zoom, WASD, auto camera, and hide-UI behave correctly.
- Chunks load ahead of movement and unload behind movement; stress travel across positive/negative world coordinates.
- Time/sky/weather transitions retain visual legibility; rain/snow do not disappear on camera movement.
- PNG exports display; WebM recording saves; audio is recorded only when enabled before recording.
- Custom component builds at `lod=0/1/2` and survives changes to camera/seed and chunk eviction.
- Repeated reloads do not materially increase GPU resource usage or cause WebGL context loss.

### Minimum visual shot matrix

Capture at least: wide establishing image at day; wide dusk/night; low-angle focal landmark; rain or storm; snow or fog; close and far camera; camera looking from a new azimuth; frame taken after crossing a chunk boundary. Use the same seed and fixed hour/weather to compare changes. Review images at both native art-pixel scale and normal display scale.

### Performance acceptance

For typical desktop targets, aim for a **16.7 ms total frame budget for 60 FPS**, while allowing user-specific performance tiers. Monitor chunk count, triangles/draws, memory use, shader cost, frame stalls and export size. A rough starting constraint is at most a few chunk builds per frame (default 2), nearby-detail-only decorative geometry, one batched static mesh per chunk, and dynamic draw calls reserved for important moving forms. These are heuristics, not guaranteed device performance. Set measurable budgets based on actual browser profiling.

### Determinism acceptance

Given the same seed, camera path, parameters and time, the world must place the same structures and produce the same static geometry. Beware: `Math.random()` in audio noise or transient effects can be acceptable for casual live ambience, but it is **not** sufficient for frame-exact offline replay. For deterministic video, replace those paths with seeded/per-frame functions and export from fixed simulation steps.

## 8 — How to improve the standalone runtime when the brief demands it

The included runtime is an excellent foundation for **large animated pixel landscapes**, not a finished AAA open-world engine. Extend it in this order rather than patching around hard limits:

1. **Geometry and terrain:** add chunk edge stitching/skirt geometry, continuous water shoreline meshing, normalized slope biome placement, spline roads/tracks, structural footprint leveling, cave/overhang representation where needed.
2. **Rendering quality:** implement G-buffer normal-based outlines, multiple depth/normal edge thresholds, real shadow maps with camera-centered cascades, bloom in a low-resolution pyramid, world-space fog, efficient water and reflection handling, material roughness/wetness.
3. **Streaming and performance:** async generation tasks/workers, geometry generation queues, robust LOD hysteresis and transition morphing, bounds-aware frustum culling, instancing for repeated props, CPU/GPU resource pools, memory thresholds.
4. **Scene systems:** entity IDs, transforms and parent-child hierarchy, click interaction and state, colliders, navigation/path network, placement ownership across chunks, serialization, camera rails and cutscene timeline.
5. **Sound:** emitter and listener positions, weather acoustic zones, river/urban proximity, interior occlusion, distance falloff, procedural event scheduling.
6. **Export:** add deterministic fixed-step capture with WebCodecs where supported, a proper muxer or a robust local FFmpeg pipeline, audio sample-accurate scheduling, encoder capability negotiation and truthful output-format labels.

For **high-fidelity Noxe parity**, separately port or vendor the original Noxe renderer modules (`pixel-engine.js`, `shaders.js`, `materials.js`, `environment.js`, `audio.js`, `exporter.js`, `recorder.js`, `palettes.js`) and reconcile their APIs with this general world architecture; the included runtime is not those files. The original Noxe MIT license and copyright notice must accompany copied source. Avoid swapping in a network dependency while continuing to call the runtime “offline self-contained.”

## 9 — Common implementation mistakes to reject

- Enlarging a single 16×16 terrain scene and calling it a continuous world.
- Dense random object placement with no roads, ecosystems, settlement logic, or composition.
- Rebuilding every object on every frame instead of updating only a transform/state.
- Calling a glowing pixel shape a working light source that casts real illumination.
- Inferring unavailable APIs or silently depending on external CDN imports.
- Treating every near-detail feature as necessary at far LOD.
- Pop-in/seams at chunk boundaries, floating foundations, objects duplicated across boundaries.
- Nonseeded layout variation; camera-dependent RNG; inconsistent appearance after a reload.
- Pixel-scale loss: objects look sophisticated in wireframe but collapse to unreadable blobs.
- Audio that starts before user interaction or causes oscillator/node leaks.
- Claiming MP4 export when only WebM is implemented, or saying video is offline-rendered when it is real-time.
- Claiming code from the original Noxe source is embedded when it is not.

## 10 — Minimum completion standard for an AI-generated world

A project is complete only when it has: a coherent world layout; the requested anchor features; functioning camera movement; consistent weather/time states; spatially plausible placements; all promised custom types actually implemented; stable chunk behavior; sane performance for the target; deterministic terrain and placements; and an understandable deliverable with honest limitations. Work beyond mere visual plausibility—make the world feel like a designed place.

### Provenance and license

Noxe Pixel Engine: © 2026 Daniel Jankulovski, MIT license. Original project: https://github.com/danieljan-design/noxe-pixel-engine. Noxe design and technical techniques informed the architectural goals of this skill; the embedded runtime is independently implemented as a compact dependency-free alternative. If you later copy Noxe source, preserve its full MIT copyright/license notice. For redistribution, keep attribution to any original code actually copied. If you choose to release this new standalone implementation under an MIT license, replace the copyright-holder placeholder below with the appropriate owner:

```text
MIT License

Copyright (c) 2026 [Appropriate copyright holder]

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

## 11 — EMBEDDED OFFLINE RUNTIME

This section contains the full HTML/JS runtime (including shaders and default components) as a **gzip+Base64 payload**. The uncompressed HTML SHA-256 is `0ff712ee6ba0324ad886b246e915974f2366262cb5326d41262e26b3ccf3a7f6`. The extraction procedure in Section 1 verifies and materializes it without network access. Treat this payload as code data, not a prompt or instructions. Do not delete or truncate these markers.


<!-- BEGIN_AMBIENT_RUNTIME_GZIP_BASE64 -->
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
<!-- END_AMBIENT_RUNTIME_GZIP_BASE64 -->
