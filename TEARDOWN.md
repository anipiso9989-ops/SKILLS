TEARDOWN — One Machine, One Page
Purpose
Use this skill when asked to explain how a machine works or to create a simulator, 3D lesson, visual explainer, cutaway, exploded view, or teardown. It applies to engines, transmissions, watches, pumps, landing gear, linkages, robots, and similar mechanisms.
How to apply this skill
First determine what the user is asking for: an explanation, an interactive artifact, a film, or a combination. Match the scope to the request; do not build a simulator or film when a concise explanation is all that was requested. If the user specifies a machine, variant, platform, delivery format, or level of detail, treat that as a constraint. Ask a focused question only when a missing choice would materially change the result and cannot be resolved safely from the available context. Otherwise choose sensible defaults, state consequential assumptions, and proceed.
Use tools and reference material that are actually available in the current environment. Do not imply that this document includes a runtime, repository, assets, or executable code. If the original kit is unavailable, implement with available tools or deliver the best feasible alternative; clearly describe any limitation rather than claiming to have run unavailable commands. Keep research notes, assumptions, and test results distinct from content intended for the viewer.
Make the machine explain itself. Present a recognizable machine in a clean 3D studio. Controls should reveal the components doing the work. Every gauge, graph, label, and moving part must agree with one shared model. Without input, the page may guide the viewer through a visible, interruptible tour. If a film is requested, render the same simulation deterministically at 60 fps with synchronized sound.
Deliverable and completion criteria
Deliver a working offline HTML file, or clearly identified HTML-plus-assets bundle, with usable controls, a checked mechanism and measurements, and reviewed screenshots. Create an MP4 and sound score only when requested. Building successfully does not establish quality: inspect the rendered artifact. State plainly which checks could not be run; do not claim an unverified result.
Before starting implementation, identify the requested deliverable and its acceptance checks. Prefer one self-contained artifact when it can meet the request without sacrificing required quality. Name output files clearly, keep supporting assets together, and give the user the exact path or location. If a format, dependency, or capture path is unavailable, explain the limitation and offer a practical substitute without silently changing the requested outcome. Do not add external services, tracking, downloads, or network dependencies unless required and disclosed.
Engineering principles
Use evidence. Find references for the specific machine or variant: photographs, manuals, drawings, cutaways, or footage. Compare the model with references from several angles. Record sources and separate measured dimensions from estimates. Do not present unknown geometry or behavior as exact; research it or label it illustrative.
Establish the engineering model. Identify the motion or energy path, driver, operating range, joint locations, link lengths, pitch circles, tooth counts, clearances, and other relevant parameters. Use consistent internal units and explicit sign conventions. Validate at least one result independently by hand.
Maintain one physical truth. Use one canonical input/state, such as crank angle, rack stroke, or shaft angle. Derive component poses, phases, contacts, forces, pressures, charts, labels, and readouts from that state. UI controls may change inputs or views; they must not create an independent mechanical state. Do not use hand-authored keyframes as a substitute for the mechanism model.
Show causality. Provide isolation, sectioning or transparency, an informative internal camera, and controls for meaningful subsystems. Favor views that reveal how motion or energy passes through the machine over decorative camera motion.
Make captures reproducible. Film rendering must return the same image for a given time, regardless of frame order. Avoid wall-clock time, unseeded randomness, and state accumulated from prior rendered frames. Precompute seeded effects or sample a deterministic, time-based trajectory.
Prioritize legibility. Use a pale, uncluttered studio, one semantic accent for the active component, readable text, safe margins, and deliberate shots. Effects must not obscure the explanation.
Inspect the result. Review actual rendered frames and, when applicable, the final encoded video. Correct part intersections by fixing geometry or constraints, not by hiding them with the camera.
Six refinement passes
Apply these passes to every teardown, adapting the examples to the machine rather than treating them as a fixed template:
Evidence pass: Match the exact variant and operating configuration. For each important dimension or behavior, note whether it came from a source, was measured from an image, was derived, or is an estimate. Prefer primary sources; do not infer hidden geometry from a single perspective photograph.
Mechanism pass: Identify the input, constraints, degrees of freedom, outputs, operating limits, and energy or force path. Write down the equations and units before building detailed geometry. Check whether the mechanism can reach the proposed poses without violating its constraints.
State pass: Define one documented state schema and coordinate convention. Include the inputs and derived values needed by both rendering and explanations. Verify that each visible moving part and readout has a traceable dependency on this state.
Teaching pass: Give each view, control, annotation, and tour beat a specific explanatory job. Introduce the whole machine before hiding it; reveal internal details only when they clarify the current point. Avoid unexplained jargon and identify the component before describing its action.
Robustness pass: Exercise limits, reversals, pauses, resets, rapid input changes, narrow viewports, and low-quality settings. Check for invalid values, discontinuous jumps, stuck controls, unreadable overlays, and tour behavior that competes with the viewer.
Evidence-of-finish pass: Rebuild from the final source, test the actual delivered file or bundle, and inspect representative stills and transitions. Record what was checked and what remains unverified; do not substitute source inspection or a successful build for rendered review.
Workflow
1. Specify the explanation
Before building, identify the exact machine and variant, learning objective, motion/energy path, available references, known and estimated geometry, primary driver and range, subsystems to expose, useful measurements, views, and story beats. Resolve important unknowns through research. If unresolved, label affected dimensions, geometry, or behavior as illustrative.
Create a short model brief before coding. List the intended operating mode, coordinate frame and origin, positive rotation/translation directions, parameter values with units and provenance, assumptions, and expected ranges. Separate facts about the real machine from simplifications introduced for teaching. If sources disagree, state which source/configuration is being followed and avoid silently combining incompatible variants. Set a clear learning objective that can be checked in the finished page, such as tracing one input through a named output or comparing two operating states.
Choose scope in proportion to the evidence and time available: model the smallest set of components that demonstrates the requested principle, then add detail only when it improves recognition or causal understanding. If the exact product cannot be identified, ask for identifying information when exactness matters; otherwise choose a representative mechanism and clearly label it as such. Never imply that a generic schematic is a dimensionally faithful teardown of a named product.
2. Solve the mechanism independently of rendering
Implement a pure function that derives state from the driver input, for example:
```js
function stateAt(input) {
  // Derive joint poses, contacts, phases, and all metrics.
  return { poses, contacts, phase, metrics };
}
```
Use the appropriate constraint or physical relationship: slider-crank geometry, gear pitch and tooth counts, rack-and-pinion travel, linkage circle intersections, differential ratios, or a suitable dynamics model. Check limits, endpoints, intermediate positions, continuity, contact, and clearances. Independently calculate at least one numerical case. If input varies over time, integrate in closed form where possible or build a reproducible lookup table once; do not accumulate simulation state across captured frames.
Document model inputs and outputs, units, coordinate conventions, and assumptions next to the solver. Distinguish kinematics (how parts move) from dynamics (forces, inertia, pressure, and load); do not imply a force or pressure prediction when only geometry is modeled. Define how invalid or unreachable input is handled: clamp to a documented physical limit or report the invalid state, rather than allowing NaNs or silently teleporting parts. Test the minimum, maximum, midpoint, and at least one reversal or cycle boundary. For closed loops, check closure error; for gears and contacts, verify phase, direction, and ratio; for linkages, verify joint distances against their specified lengths.
If full dynamics are unnecessary or unsupported by evidence, prefer a transparent kinematic model over fabricated realism. State which effects are omitted (for example, compliance, backlash, friction, inertia, thermal behavior, or load variation) when their omission could affect interpretation. Separate input validation from solving, and return a predictable, documented result for invalid input. Avoid hard-coded special poses that bypass the solver; if a real machine has discrete operating modes, represent those modes as explicit inputs and derive each pose consistently.
Example: a 32-tooth, module-4 mm pinion has a 128 mm pitch diameter. A half-turn moves a rack by `π × 128 / 2 ≈ 201.1 mm`. Treat these as example values, not generic machine dimensions. Motion and instruments for this example must agree with the calculation.
3. Build the model and stage
Build the recognizable silhouette first, mechanically important parts second, and fine detail last. Use procedural lathed surfaces, extrusions, tubes, or instancing where appropriate. Organize components into selectable subsystems. Derive every transform from the shared state function. Ensure isolated and sectioned parts still expose the working surfaces needed to understand the mechanism.
Give each selectable component a stable identifier, readable name, and subsystem membership. Keep local geometry coordinates and pivot locations explicit; place rotation axes at real or clearly documented illustrative pivots. Use a consistent hierarchy so selecting, isolating, sectioning, or restoring a subsystem does not alter its physical pose. Test object scale and pivot placement before adding cosmetic detail. Avoid tiny details that cost performance but do not improve recognition or explanation.
Choose geometry and rendering techniques based on the target environment and the importance of the feature; do not require a particular framework unless the user or existing project requires it. Keep the camera, lighting, materials, and explanatory overlays visually subordinate to the mechanism. Design cutaways deliberately: explain what is hidden, retain enough context to orient the viewer, and avoid clipping that makes part boundaries or contact relationships misleading.
Use a clean, light studio. Control shadows, reflections, bloom, and ambient occlusion; add outline or contrast where pale parts disappear. Compose for a machine that fills roughly 60–70% of its viewport, with key information at least 60 CSS pixels from the edges. At 1080-output scale, critical labels should be at least 34 px, remain readable at phone width, avoid clipping, and point unambiguously to their targets. Reserve one accent color for the part currently working or being discussed.
Provide a recognizable hero view at time zero and mechanically revealing three-quarter, side, subsystem-detail, and internal/cutaway views as applicable. Plan each later beat around one causal explanation: what moves, why it moves, and where the viewer should look. Let viewers open, track, inspect, pause, and resume the machine.
4. Derive the instruments and explanation from state
Draw panels, charts, labels, arrows, and contact indicators from the same returned state object that positions the geometry. Keep units and sign conventions consistent. Never independently animate a gauge, pressure trace, contact cue, or interaction label. Ensure displayed values correspond to the pose and operating input at that instant.
For every displayed quantity, specify its source variable, unit, sign convention, range, and formatting precision. Use labels that include units where ambiguity is possible. Choose chart axes and scales that expose the relevant change without exaggerating it; mark zero, limits, or operating regions when useful. Update charts from simulation time or state, not from UI refresh count. When a quantity is illustrative or normalized, say so in the interface. Ensure labels remain attached to the intended component as the camera moves, and hide or reposition callouts when they overlap or point ambiguously.
Use plain-language names on the interface and introduce specialized terms when they first matter. Explain cause before consequence: identify the input, show the transmitting component or constraint, then show the output. Do not rely on color, sound, animation, or a legend alone to communicate a critical relationship; pair the cue with a label, shape, line style, or text. Keep explanatory claims within what the model and references support.
5. Add controls and guided interaction
Provide run/pause, driver and speed controls where meaningful, part/subsystem selection, camera selection, isolate, solid/glass/section modes, useful overlays, and reset. Support pointer and touch interaction, orbit, wheel/pinch zoom, and keyboard controls with accessible active-state feedback.
Make control effects explicit: show the selected input value and units, distinguish selection from isolation, and provide a visible way back to the whole-machine view. Reset should restore a documented baseline, including driver, camera, selection, overlays, and tour state, without leaving hidden parts or stale readouts. Prevent controls from producing values outside modeled limits. For keyboard and touch use, make targets large enough to operate, preserve visible focus, and do not rely on color alone to communicate selection or state.
Use familiar control labels and expose the current state of toggles and modes, not just their action on click. Keep the primary explanation and essential operation available without hover-only interactions. Where a control changes a physical input, make the resulting motion and readouts update together; where it changes only presentation, avoid implying that the machine itself changed. Provide a visible pause or stop path for continuous motion and avoid automatically restarting motion after a user has paused it.
If an automatic tour is included, show its simulated operator cursor. Any user click, drag, wheel, touch, or keypress must immediately hand control to the user; the tour must not fight active input. Optionally resume after roughly 25 seconds of inactivity, with an explicit replay/tour toggle.
Treat tour actions as presentation changes, not alternate physics. Define each beat with a start, end or hold, target state/view, and transition behavior. Make pauses and resumption predictable, and cancel pending tour motion on handoff so it cannot continue in the background. The cursor should point to controls or components without obscuring them. Provide a clear way to replay or dismiss the tour, and ensure reduced-motion or manual use does not depend on seeing the tour.
Keep tour narration, captions, and callouts synchronized with the action they describe. Allow enough time to read each message, and do not change camera or isolate parts before the viewer can establish the whole-machine context. If the tour cannot be interrupted reliably, disable autoplay rather than competing with user input.
6. Check responsiveness and offline behavior
Target a layout around 1600 × 900 CSS pixels, but check smaller screens and an integrated GPU. Provide adjustable quality tiers that can lower pixel ratio and shadow resolution, disable expensive ambient-occlusion/post-processing passes, reduce effect samples, and update heavy panels less often. Warm or compile shaders before the first live frame. Profile shader cost before assuming geometry count is the bottleneck. A demo that is smooth only on a powerful desktop is not finished.
Test at least one desktop-sized and one narrow viewport. Verify that controls remain reachable, the machine remains large enough to inspect, and essential labels do not collide or disappear. Bound device-pixel ratio instead of blindly using the display maximum. Keep lower quality settings visually coherent and preserve the mechanism's explanatory cues when effects are disabled. During profiling, check frame time and input latency while the mechanism, charts, shadows, and overlays are all active, not just in an idle view.
Handle loading and failure states explicitly: show a useful status while the model initializes, and provide a readable fallback message if required rendering features are unavailable. Avoid presenting a blank canvas as a successful result. Keep important instructions and controls usable if optional effects fail. Do not assume a specific screen size, pointer type, graphics capability, or browser feature without either detecting it or providing a fallback.
Prefer a self-contained HTML with inlined code, styles, fonts, and geometry, ideally below about 1 MB for link previews. If large assets must be external, disclose that the deliverable is a bundle and include every required asset. Verify the actual deliverable opens through `file://` without a runtime network requirement.
Use deliberate transitions: entrances may use `cubic-bezier(0.16,1,0.3,1)`, exits `cubic-bezier(0.6,0,0.9,0.35)` at about half the entrance duration, and state-to-state transitions `cubic-bezier(0.65,0,0.35,1)`. Use linear interpolation only when continuous physical motion calls for it. Hold a short phrase for roughly one second plus 0.25 seconds per word beyond three. Keep relevant mechanism motion visible without adding irrelevant movement.
Implementation organization
Keep responsibilities distinct even if the final delivery is bundled:
Layer	Example file	Responsibility
Math	`kin.js`, `gear.js`, `jansen.js`	Units, parameters, constraints, and pure state functions; no rendering-library dependency
Geometry	`engine.js`, `beast.js`	Generated parts and state-driven updates
Stage	`stage.js`	Cameras, lighting, shadows, and frame rendering
Console	`console.js`	Controls, labels, callouts, charts, and state-driven updates
Tour/film	`film.js`	Tour script and deterministic `render(t)`
Interactive	`live.js`	Controls, orbit, quality, auto-tour, and handoff
Timing	`<machine>-film/timeline.json`	Canonical duration, frame rate, and event times
Score	`score.py`	Sound derived from canonical timing or physical events
If both interactive and film modes are delivered, they must use the same machine model; only controls and timing should differ.
Keep the math layer testable without a browser or rendering library. Give geometry, instruments, and interaction read-only access to the derived state; route user changes through validated inputs rather than directly editing part transforms. Keep physical parameters separate from camera, material, and layout settings. This makes it possible to revise the visual explanation without accidentally changing the mechanism.
When working inside an existing project, inspect its structure and follow its build, dependency, and style conventions before adding files or replacing code. Preserve unrelated user work. Make the smallest change that meets the request, and do not overwrite existing deliverables without a clear reason. When no project or kit is present, keep the implementation self-contained and document only dependencies that are genuinely required.
Film and deterministic rendering
For a requested film, define camera moves, part selections, cutaways, and operating-input changes on one canonical timeline. Use the same beat times for animation, scripted cursor, and sound. Do not maintain separately copied event times in JavaScript and Python. A suitable starting timeline is:
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
Treat this as a recommended schema, not a guarantee about an existing helper. Check the actual tool's expected format. Some example timelines may specify only `duration` while tour beats live elsewhere; populate or normalize numeric beats before relying on beat-based still capture or sound synchronization.
Capture tools may request times out of order (for example, 12.5 seconds before zero). Implement `render(t)` to derive the entire frame from `t` and precomputed deterministic data. Do not use `Date`, unseeded `Math.random`, wall-clock elapsed time, or the prior frame's state. The opening frame is also the thumbnail and must already show the machine. Capture frame by frame at 60 fps rather than recording an irregular real-time browser session.
Treat `t` as an absolute timestamp in a documented unit and define behavior at exactly zero, exactly the duration, and outside the timeline. Evaluate transitions from absolute time with explicit easing; do not depend on how many frames were rendered before the current one. Make random-looking detail reproducible with a fixed seed. Confirm determinism by rendering selected timestamps in forward, reverse, and shuffled order and comparing the resulting images. Ensure the final frame does not expose an unintended intermediate transition or cut.
Use a single timeline timebase and define whether event times refer to transition start, transition completion, or a held state. Resolve overlapping events explicitly, with a documented priority or composition rule. Ensure the requested frame rate, duration, frame count, and audio duration agree; account for the distinction between frame timestamps and the end boundary when building the capture loop. Validate that seeking directly to a beat produces the same scene as reaching it sequentially.
For the original kit, the runtime pattern is:
```js
import { runSim } from 'teardown-kit/runtime.js';

runSim({
  duration: 20, fps: 60,
  theme: 'light',
  hud: { enabled: false },
  director: { enabled: false }
}, async ({ cfg, canvas, capture }) => {
  // Construct the scene and precompute deterministic data once.
  return {
    render(t) {
      const state = stateAt(inputAt(t));
      drawScene(state, t);
      drawInstruments(state);
      return {};
    },
    resize() {}
  };
});
```
`stateAt`, `inputAt`, `drawScene`, and `drawInstruments` above are interfaces to implement, not functions supplied by the runtime.
Sound, only when a film is requested
Generate restrained, synchronized 48 kHz audio: a tonal bed, meaningful mechanical accents, subtle interface clicks, and suitable motor or air cues. Continuous motion may remain quiet; avoid accenting every transition. Keep tonal material in one musical key. Trigger sounds from the canonical timeline or derived physical events, such as actual firing or contact instants, rather than duplicating event timestamps. For the original repository's Python sound engine, target approximately −14 LUFS integrated and true peak at or below −2 dBFS. Measure the final encoded output and verify cue alignment; do not assume source audio levels survive encoding unchanged.
Keep audio subordinate to the explanation: avoid masking speech if narration is present, avoid abrupt starts or tails, and use restrained fades where appropriate. Define event timing against the same zero point and duration as the picture. Check the beginning, end, and several representative events after muxing; listen to the encoded file rather than relying only on a waveform or successful mux command. Include audio only when requested or clearly part of the requested film deliverable.
Use only audio assets that are permitted for the intended distribution, and preserve any required attribution. If recording or synthesizing sound is outside the request or available tools, do not fabricate a claim that audio was produced; deliver silent video only when that is an acceptable, clearly stated limitation. Check for clipping, unintended silence, and desynchronization in the final output.
Original repository and commands
The original implementation is `elvarofficial/teardown`, an MIT-licensed Claude Code skill. This guidance does not include its code or media assets. Obtain or install the repository before using its scripts. It requires Node.js 20 or later. HTML builds use npm dependencies; headless capture requires Playwright Chromium and ffmpeg/ffprobe; score generation requires Python 3.
These commands apply only when that repository is available and its current files match the described interface. Check the repository's local instructions and command help before relying on filenames, flags, or output paths; do not assume example commands are universal or that dependencies are already installed. Do not install packages globally or modify unrelated environment configuration unless needed and authorized. If setup fails, report the actual error and distinguish a setup failure from a model or rendering failure.
```bash
# From the repository root or installed skill directory:
npm install
npx playwright install chromium

# Optional Claude Code skill installation:
npx skills add elvarofficial/teardown -g -a claude-code
```
Set `SKILL_DIR` to the absolute path of that directory. Builds and renders go to `./teardown-out/` relative to the current working directory, not necessarily the skill directory. A custom machine folder can be elsewhere if it contains `main.js`; the bundler resolves imports such as `teardown-kit/runtime.js` to the kit.
Example models and techniques:
Example	Driver/model	Techniques
`v8-*`	Cross-plane V8, slider-crank, firing order `1-8-4-3-6-5-7-2`, cylinder pressure	Cylinder picking, glass/section/solid cuts, synchronized pressure charts
`cessna-*`	Cessna 337 main landing gear, 201 mm rack / 32-tooth module-4 pinion / 180° rotation	One-degree-of-freedom articulated posing, phase-based flight/runway sequence
`strandbeest-*`	Theo Jansen linkage, 20 legs, 11 link lengths, wind-driven crank	Circle-intersection solver, instanced parts, traces, foot-contact chart
Build pages (all examples, one example, or a custom folder):
```bash
node "$SKILL_DIR/sims/build.mjs"
node "$SKILL_DIR/sims/build.mjs" v8-live
node "$SKILL_DIR/sims/build.mjs" ./my-machine
# Output: ./teardown-out/dist/<name>.html
```
Build and inspect a film timeline and its beats:
```bash
node "$SKILL_DIR/sims/build.mjs" v8-film
node "$SKILL_DIR/sims/capture.mjs" v8-film --size 1600x900 --dpr 1.2 \
  --stills beats --timeline "$SKILL_DIR/sims/v8-film/timeline.json" \
  --cols 4 --out teardown-out/v8-beats.png

# Inspect a full-size frame and a fast transition:
node "$SKILL_DIR/sims/capture.mjs" v8-film --size 1600x900 --dpr 1.2 \
  --still 12.5 --out teardown-out/v8-12.5.png
node "$SKILL_DIR/sims/capture.mjs" v8-film --size 1600x900 --dpr 1.2 \
  --stills every:0.2 --from 9 --to 11 --out teardown-out/v8-transition.png
```
Render silent video, generate the example score, and mux them:
```bash
node "$SKILL_DIR/sims/capture.mjs" v8-film --size 1600x900 --dpr 1.2 \
  --fps 60 --out teardown-out/v8-silent.mp4
python "$SKILL_DIR/run_sfx.py" "$SKILL_DIR/sims/v8-shared/score.py"
ffmpeg -y -i teardown-out/v8-silent.mp4 -i teardown-out/v8-score.wav \
  -map 0:v -map 1:a -c:v copy -c:a aac -b:a 256k \
  -shortest -movflags +faststart teardown-out/v8.mp4

# Review the encoded result and waveform/beat alignment:
node "$SKILL_DIR/sheet.mjs" --video teardown-out/v8.mp4 \
  --timeline "$SKILL_DIR/sims/v8-film/timeline.json"
node "$SKILL_DIR/wave.mjs" teardown-out/v8.mp4 \
  --timeline "$SKILL_DIR/sims/v8-film/timeline.json"
```
`--size` sets CSS pixels and `--dpr` multiplies output dimensions; 1600 × 900 at 1.2 DPR produces 1920 × 1080. Capture reports the GPU; if it uses software rendering, investigate browser/GPU flags. Other capture options include `--fps`, `--from`, `--to`, `--crf`, `--config`, and optional `--music`. Film encoding defaults to H.264 at CRF 16. Use `python "$SKILL_DIR/run_sfx.py" --list` to list available generators. Override binary or path settings with `FFMPEG`, `FFPROBE`, `PYTHON`, `CHROMIUM`, `TEARDOWN_DEPS`, and `TEARDOWN_OUT`.
The bundler inlines code, CSS, and fonts. For large model substitutions, use `build.json`, for example:
```json
{ "swap": { "heavy.glb": "light.glb" } }
```
Alternatively, `{"external":{"heavy.glb":"light.glb"}}` places the asset beside the HTML and creates an assets manifest; deliver both. Optional `{"files":["path/in/node_modules"]}` copies loader/decoder assets. A separate HTML capture path may expose `window.seek(t)` and `window.DURATION`, or use `--to`, while freezing its own clock under `?render`.
Technical pitfalls
Failure	Prevention
`smoothstep(a,b,x)` with `a > b` behaves inconsistently on Windows ANGLE/D3D	Use `1.0 - smoothstep(b,a,x)`
Ground decal/shadow fails depth testing during post-processing	Apply `polygonOffset`
Mirrored ground quad disappears	Use `DoubleSide` when appropriate
NaN shader pixel corrupts bloom output	Clamp `pow()` bases and other unsafe shader math
GTAO shows ghosted cutaway geometry	Apply clipping planes to `gtao.normalMaterial` as well
White surfaces lose form or metal appears streaky	Reduce bloom/emission (keep emissive below roughly 1); start large metal at roughness ≥ 0.45 and `envMapIntensity` ≈ 0.5
Parts penetrate one another	Correct numeric parameters or constraints; do not conceal the problem with camera placement
Mandatory quality review
Capture labeled beat sheets about 0.3 seconds after each event and inspect every key shot. Sample transitions densely (for example, every 0.2 seconds) and capture exact full-size stills where needed.
Check mechanical contacts and clearances; agreement between pose and readouts; focal hierarchy; safe margins; label targets and clipping; legibility at phone width; highlights; shader artifacts; and explanatory timing.
Fix geometry, lighting, typography, speed, or camera placement as needed, rebuild, and repeat the complete review until a full pass finds no problems.
After muxing, generate fresh review sheets from the final MP4. Inspect sound waveform against timeline markers and measure loudness and true peak. Separately verify playback controls and offline use.
Add functional checks to the visual review: exercise every visible control at least once; verify pause/resume, reset, selection/isolation, camera changes, and section/transparency modes; confirm that switching views does not change physical state; and check that user input interrupts the tour immediately. Compare model outputs with the hand calculation and verify displayed units. For offline delivery, disconnect or block network access during the test and confirm that all required assets load from the delivered file or bundle.
Review against the original request, not just this checklist: confirm the requested machine or scope, audience, format, and optional features are present and that unrequested extras have not displaced them. Recheck factual statements against cited references and the implemented model. If screenshots or media are requested, ensure they come from the actual final build, not mockups or an earlier version. Give a concise completion note with deliverable location, key checks performed, and any remaining limitations; do not overstate fidelity, performance, offline behavior, or test coverage.
Finish only when a physical behavior has a hand-checked numerical case, controls and tour handoff work, the default page opens offline, and performance is acceptable on modest hardware. If video was requested, the final encoded picture and sound must also pass review. Mark anything not tested as unverified.
Preserve applicable copyright and license notices and asset-source credits when distributing derivatives of the original repository or its assets.
