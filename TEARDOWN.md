---
name: teardown
description: >-
  Build reference-accurate interactive 3D teardowns of real machines: an offline
  HTML simulator with coherent mechanism motion, live instruments, cutaways,
  subsystem controls, camera views, and an interruptible auto-tour. Optionally
  record a deterministic 60 fps film with synchronized procedural sound.
---

# TEARDOWN — One Machine, One Page

## Purpose and definition of done

Use when asked **how a machine works**, or for a *simulator, 3D lesson, visual explainer, cutaway, exploded view,* or *teardown*. Applicable to engines, transmissions, watches, pumps, landing gear, linkages, robots, etc.

**Make the machine explain itself.** It runs in a clean 3D studio. Controls reveal the component doing the work. Every gauge, graph, label, and moving part is driven by the same mathematical model. Unattended, the page tours itself with a visible operator cursor; user input takes over immediately. If requested, record the same simulation as a frame-accurate 60 fps film with synchronized audio.

**Deliver:** A working offline HTML file (or an explicitly identified HTML-plus-assets bundle), usable controls, checked mechanism and measurements, and reviewed screenshots. Add MP4 and score only when requested. A successful build is **not** proof of quality. This file is a complete *instruction specification*; implementing a simulator still requires code and relevant tools.

## 1. Core rules

1. **Reference, don't invent.** Start with a photograph, manual drawing, cutaway, or footage of the *specific* machine. Compare the rendered mechanism against it at several angles. Document sources and distinguish exact dimensions from estimates.
2. **Real dimensions, real equations.** Establish bore/stroke, pitch circles, tooth counts, pivot coordinates, link lengths, ranges, and clearances. Use consistent units internally; validate at least one result by hand.
3. **One mechanical state.** Drive all components with a canonical input/state (crank angle, rack stroke, wind-derived shaft angle, etc.). The model—not keyframes—determines poses, contacts, phases, forces, pressures, charts and readouts. UI interactions can change the input or select a view but must not create a second physical truth.
4. **Show the mechanism.** Provide isolation, section or transparency, an informative inside camera, and controls for meaningful subsystems. Prefer the view revealing causality to decorative orbits.
5. **Keep it deterministic.** Film frames must render identically for any requested time, in any order. Precompute seeded or time-integrated effects; never advance physics based on prior frames during capture.
6. **Prioritize legibility.** Pale studio, one semantic accent for the active part, readable type, no overlapping callouts, deliberate shots. Reject visual effects that obscure the explanation.
7. **Verify the artifact.** Inspect actual rendered frames and final encoded video. Fix intersections by changing geometry or constraints, not by moving the camera to hide them.

## 2. Engineering workflow

**A. Specify.** Identify the exact machine/variant, learning objective, energy/motion path, real references, measured geometry, primary driver, operating range, parts to expose, views, useful metrics, and story beats. If a parameter is unknown, research it; if it remains unknown, label the geometry/behavior **illustrative** rather than presenting it as an exact reconstruction.

**B. Solve without rendering.** Implement a pure kinematic/physical function such as:

```js
function stateAt(input) {
  // Derive joint poses, contacts, mechanical phases, and all metrics.
  return { poses, contacts, phase, metrics };
}
```

Use constraint geometry—slider-crank, gear teeth and module, rack/pinion, linkage circle intersections, differential relationships, or appropriate dynamics. Validate endpoints, intermediate motion, continuity, contact, limits, and a numerical case. If a control varies over time, integrate once in closed form or into a reproducible lookup table; sample that table rather than accumulating across rendered frames.

Example check: a **32-tooth, module-4 mm pinion** has pitch diameter `32 × 4 = 128 mm`; 180° of rotation consumes rack travel `π × 128 / 2 ≈ 201.1 mm`. Animated stroke and instrumentation must agree with this result. These are example-machine numbers, not generic dimensions.

**C. Model.** Build the recognizable silhouette first, then mechanically important parts, then details. Prefer procedural lathed surfaces, tubes/extrusions, or instanced repeating parts when sensible. Organize parts into selectable subsystems; update their transforms from `stateAt(input)` only. Give isolated/sectioned parts suitable geometry and materials so their working surfaces remain visible.

**D. Instrument.** Draw panels, charts, labels, arrows, and contact indicators from the **same returned state object** used to pose the geometry. Units and sign conventions must remain consistent. Never independently animate a gauge, pressure trace, or interaction label.

**E. Explain.** Select an opening hero shot where the machine is recognizable **at time zero**. Plan each later beat around one causal explanation: what moves, why, and what to look at. Let the viewer open, track, inspect, pause and resume the machine.

## 3. Visual and interaction specification

| Area | Requirement |
|---|---|
| Stage | Clean light studio; control shadows, reflections, bloom and ambient occlusion; near-white parts need outlines/contrast |
| Composition | Prefer a `1600 × 900` CSS layout; machine fills ~60–70% of its viewport; keep key information ≥60 px from edges |
| Text | Critical labels ≥34 px at 1080-output scale; readable at phone width; no clipping or labels crossing their targets |
| Color | One accent, reserved for what is changing/working right now |
| Camera | Hero, mechanically revealing 3/4/side, detailed subsystem and internal/cutaway views; optimize for visible motion paths |
| Controls | Run/pause, driver/speed, part selector, camera selector, isolate, solid/glass/section, useful overlays, reset |
| Navigation | Pointer/touch, orbit, wheel/pinch zoom, keyboard shortcuts, accessible active-state feedback |
| Autoplay | Guided tour with visible simulated cursor; **any user click, drag, wheel, touch or key takes control instantly** |
| Idle | Optionally resume tour after ~25 s without input; provide explicit replay/tour toggle; never fight active interaction |
| Offline | Prefer a truly self-contained HTML with inlined JS/CSS/fonts/geometry, aiming below ~1 MB for link previews |

For large models, it is acceptable to distribute assets beside the HTML, **but disclose that it is no longer one standalone file**. Verify the actual artifact works through `file://`, with no runtime network requirement.

Motion timing: entrances `cubic-bezier(0.16,1,0.3,1)`; exits `cubic-bezier(0.6,0,0.9,0.35)` at about half the entrance duration; state-to-state `cubic-bezier(0.65,0,0.35,1)`. Only use linear time interpolation where continuous physical motion calls for it. Hold a short phrase for ~1 s plus ~0.25 s per word past three. Keep some mechanism visibly active, but never add irrelevant motion.

**Performance:** Test on an integrated GPU and on smaller screens. Include adjustable quality tiers: reduce pixel ratio and shadow size, drop expensive AO/post passes, lower effect samples, and redraw heavy panels less often. Warm/compile shaders before the first live frame. Profile shader cost before assuming that geometry count is the problem. A 60 fps desktop demo that runs at 15 fps on a laptop is not finished.

## 4. Interactive and film implementations

Organize responsibilities even if final delivery is bundled:

| Layer | Typical file | Responsibility |
|---|---|---|
| Math | `kin.js`, `gear.js`, `jansen.js` | Units, parameters, constraints and pure `stateAt(...)`, with no three.js dependency |
| Geometry | `engine.js`, `beast.js` | Code-generated parts and `update(state)` |
| Stage | `stage.js` | Cameras, lighting, shadows and `frame(state,cam)` |
| Console | `console.js` | Buttons, labels, callouts, charts and state-driven `update(state)` |
| Tour | `film.js` | Operator script and deterministic `render(t)` |
| Interactive | `live.js` | Controls, orbit, quality, auto-tour and input handoff |
| Timing | `<machine>-film/timeline.json` | Canonical duration, frame rate and action times |
| Score | `score.py` | Sound derived from the canonical timeline/physical events |

In the original example kit each machine has `<name>-shared/`, `<name>-film/` and `<name>-live/` folders. Keep the **same machine model** in both modes; only the controls and timing differ.

### Film contract

The film script defines camera moves, part selections, cutaways and operating-input changes. **One canonical timeline** supplies every beat time to animation, scripted cursor and score. Do not maintain separate, hand-copied event times in JavaScript and Python. An illustrative timeline for a new implementation:

```json
{
  "duration": 20,
  "fps": 60,
  "beats": [
    { "t": 0, "action": "hero" },
    { "t": 4, "action": "isolate", "part": "mechanism" },
    { "t": 9, "action": "section", "part": "mechanism" },
    { "t": 15, "action": "return-hero" }
  ]
}
```

This is a recommended schema for new work; match the actual helper's expected format if using the repository scripts. The shipped example timelines may contain only `duration` even though tour beats exist elsewhere: **populate/normalize numeric beats before relying on `--stills beats` or sound synchronization**.

A capture tool may request frame `t=12.5` before `t=0`. Therefore `render(t)` must not use `Date`, unseeded `Math.random`, elapsed wall-clock time or the previous frame's state. Sample a deterministic control trajectory and render from scratch. For the original kit, the public contract is:

```js
import { runSim } from 'teardown-kit/runtime.js';

runSim({
  duration: 20, fps: 60,
  theme: 'light',
  hud: { enabled: false },
  director: { enabled: false }
}, async ({ cfg, canvas, capture }) => {
  // Construct scene and precompute deterministic data once.
  return {
    render(t) {
      const state = stateAt(inputAt(t)); // Implement these functions.
      drawScene(state, t);               // Implement these functions.
      drawInstruments(state);
      return {};
    },
    resize() {}
  };
});
```

`stateAt`, `inputAt`, `drawScene` and `drawInstruments` are **interfaces you implement**, not functions included in the kit. Frame zero is the thumbnail; it must already show the machine. Capture the film frame by frame at 60 fps rather than recording an irregular real-time browser session.

### Sound contract

For film only: synthesize restrained, synced **48 kHz** audio (tonal bed, mechanical accents, subtle interface clicks, motor/air cues). Continuous motion can remain quiet; accents should mark meaningful work, not narrate every transition. Keep tonal sounds in one musical key. Trigger cues from the same timeline or derived physical state (e.g., actual firing/contact instants), not duplicate timestamps. The repository's Python engine uses standard-library DSP and optional CC0 recorded samples. Target approximately **−14 LUFS integrated** with **true peak ≤ −2 dBFS**; verify the *final* encoded output and cue alignment.

## 5. Original repository: setup, examples and commands

The original implementation is [elvarofficial/teardown](https://github.com/elvarofficial/teardown), an MIT-licensed Claude Code skill. This document contains its full working guidance; its **code and media assets are not embedded here**. To use its scripts, clone/download or install it first. Requires **Node.js 20+**. HTML builds use `npm` dependencies; headless captures need Playwright Chromium and **ffmpeg/ffprobe**; score generation needs **Python 3**.

```bash
# From the repository root (or installed skill directory):
npm install
npx playwright install chromium

# Optional Claude Code skill installation:
npx skills add elvarofficial/teardown -g -a claude-code
```

Set `SKILL_DIR` to that directory's **absolute path**. Builds/renders go to `./teardown-out/` relative to the *current working directory*, not necessarily the skill directory. A custom machine folder may live elsewhere if it has `main.js`; the bundler resolves imports such as `teardown-kit/runtime.js` to the kit.

| Example | Model driver | Techniques to borrow |
|---|---|---|
| `v8-*` | Cross-plane V8, slider-crank, firing order `1-8-4-3-6-5-7-2`, cylinder pressure | Cylinder picking, glass/section/solid cuts, synchronized pressure charts |
| `cessna-*` | Cessna 337 main landing gear, 201 mm rack / 32-tooth module-4 pinion / 180° rotation | One-DOF articulated posing, phase-based flight/runway |
| `strandbeest-*` | Theo Jansen linkage: 20 legs, 11 link lengths, wind-driven crank | Circle-intersection solver, instanced parts, traces and foot-contact chart |

```bash
# Build pages (all examples, one example, or custom folder):
node "$SKILL_DIR/sims/build.mjs"
node "$SKILL_DIR/sims/build.mjs" v8-live
node "$SKILL_DIR/sims/build.mjs" ./my-machine
# Output: ./teardown-out/dist/<name>.html

# Build a film and inspect timeline beats:
node "$SKILL_DIR/sims/build.mjs" v8-film
node "$SKILL_DIR/sims/capture.mjs" v8-film --size 1600x900 --dpr 1.2 \
  --stills beats --timeline "$SKILL_DIR/sims/v8-film/timeline.json" \
  --cols 4 --out teardown-out/v8-beats.png

# Check one full frame and a fast transition:
node "$SKILL_DIR/sims/capture.mjs" v8-film --size 1600x900 --dpr 1.2 \
  --still 12.5 --out teardown-out/v8-12.5.png
node "$SKILL_DIR/sims/capture.mjs" v8-film --size 1600x900 --dpr 1.2 \
  --stills every:0.2 --from 9 --to 11 --out teardown-out/v8-transition.png

# Render silent video, render the example sound score, then mux:
node "$SKILL_DIR/sims/capture.mjs" v8-film --size 1600x900 --dpr 1.2 \
  --fps 60 --out teardown-out/v8-silent.mp4
python "$SKILL_DIR/run_sfx.py" "$SKILL_DIR/sims/v8-shared/score.py"
ffmpeg -y -i teardown-out/v8-silent.mp4 -i teardown-out/v8-score.wav \
  -map 0:v -map 1:a -c:v copy -c:a aac -b:a 256k \
  -shortest -movflags +faststart teardown-out/v8.mp4

# Inspect the actually encoded result and the waveform/beat alignment:
node "$SKILL_DIR/sheet.mjs" --video teardown-out/v8.mp4 \
  --timeline "$SKILL_DIR/sims/v8-film/timeline.json"
node "$SKILL_DIR/wave.mjs" teardown-out/v8.mp4 \
  --timeline "$SKILL_DIR/sims/v8-film/timeline.json"
```

`--size` sets CSS pixels and `--dpr` multiplies output: `1600×900` at `1.2` produces `1920×1080`. Capture prints the GPU; a software renderer warrants browser/GPU-flag investigation. Other capture options include `--fps`, `--from`, `--to`, `--crf`, `--config` and optional `--music`. Film encoding defaults to H.264, CRF 16. `python "$SKILL_DIR/run_sfx.py" --list` lists available generators. Override binaries/paths with `FFMPEG`, `FFPROBE`, `PYTHON`, `CHROMIUM`, `TEARDOWN_DEPS` and `TEARDOWN_OUT`.

The bundler inlines code, CSS and fonts. For large model swaps, a machine folder can use `build.json`:

```json
{ "swap": { "heavy.glb": "light.glb" } }
```

Or use `{"external":{"heavy.glb":"light.glb"}}` to place the asset beside the HTML (plus an assets manifest), and deliver **both**. Optional `{"files":["path/in/node_modules"]}` copies loader/decoder assets. A separate HTML capture path may instead expose `window.seek(t)` and `window.DURATION` (or use `--to`) while freezing its own clock under `?render`.

## 6. Quality-control traps and final review

**Specific WebGL/three.js pitfalls:**

| Failure | Prevention |
|---|---|
| `smoothstep(a,b,x)` with `a > b` breaks on Windows ANGLE/D3D | Use `1.0 - smoothstep(b,a,x)` |
| Ground decal/shadow fails depth test during post-processing | Apply `polygonOffset` |
| Mirrored ground quad vanishes | Use `DoubleSide` when appropriate |
| A NaN pixel destroys bloom output | Clamp `pow()` bases and all unsafe shader math |
| GTAO shows ghost cutaway geometry | Apply clipping planes to `gtao.normalMaterial` too |
| White surfaces lose form / metal becomes streaky | Reduce bloom/emission (emissive below ~1); start large metal at `roughness ≥ 0.45`, `envMapIntensity ≈ 0.5` |
| Parts penetrate one another | Change the numeric parameters/constraints—not the camera |

**Mandatory inspection loop:**

1. Capture labeled beat sheets about **0.3 s after each event**; verify all key shots. Inspect dense `every:0.2` samples through transitions and exact full-size stills where necessary.
2. Check mechanical contacts and clearances, readout/pose agreement, one clear focal point, safe margins, label clipping, key information at phone width, highlights, shader artifacts, and legible timing.
3. Fix geometry, lighting, typography, speed and camera placement; rebuild; repeat the **whole** review loop until a complete pass finds no problems.
4. After muxing, make fresh sheets from the **final MP4**. Inspect sound waveform versus timeline markers and measure loudness/true peak. Verify playback controls and offline use separately.

**Stop only when:** physical behavior has a hand-checked numerical case; interactive controls and auto-tour handoff work; the default page opens offline; live performance is acceptable on modest hardware; and, when video was requested, final encoded picture and sound pass inspection. Mark any test you could not run **unverified** rather than claiming success.

## Attribution

Source: [elvarofficial/teardown](https://github.com/elvarofficial/teardown), MIT. The kit, capture, audio engine and review methodology derive from [whaleyxbt/claude-motion](https://github.com/whaleyxbt/claude-motion) (MIT). Optional CC0 Freesound samples were prepared by [Barty-Bart/motion-graphics](https://github.com/Barty-Bart/motion-graphics) (MIT). Preserve relevant copyright/license notices and asset-source credits when distributing derivatives.

*Consolidated from the repository's `README.md`, `SKILL.md`, CLI/help comments and implementation contracts. Reviewed against `main` tree `a91262ac1109051e014662d42ecb0b7bc1f113b1` on 2026-11-01.*
