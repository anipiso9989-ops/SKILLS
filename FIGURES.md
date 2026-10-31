---
name: teachable-figures
version: single-file-1.0
source: https://github.com/ywEdAi/teachable-figures
source-revision: main (inspected 2026-10-31)
description: Design, implement, and verify one self-contained interactive teaching figure at a time. Guide a learner through predict → try → explain, using an accurate causal model and a renderer chosen for the subject. Standalone output needs no repository files, installed rendering kit, build tool, package, font, CDN, or external script.
---

# FIGURES — self-contained interactive teaching figure skill

> **Single-file operating contract.** This document is the complete skill entry point, specification, working reference implementation, renderer recipes, quality gates, and provenance. **Never import from `references/`, `assets/`, `kit/`, `scripts/`, `examples/`, or `tests/`**: those directories are *not part of this distribution*. Do not tell the user to clone a repository, copy a toolkit, install NPM packages, or consult another skill file. Implement outputs from the rules and inline code here, using ordinary browser primitives. If the host application already has components or tests, integrate with those instead of creating an incompatible parallel framework. External **source facts, images or 3D geometry** can still be necessary for specialized subject fidelity; explain that content requirement rather than pretending it is supplied by this skill. A user-provided reference is not a missing skill dependency.

## The job in one sentence

Make a single mechanism **predictable, operable, observable, and explainable**: the learner predicts what changing a cause will do, changes that cause, observes the consequence, and explains the governing relationship—including one transfer case.

**Authority order:** user goal and fidelity requirements → host application's real contracts → subject facts and valid model → teaching loop → renderer. **The model owns the truth; the visual, animation, accessible description, and numeric readout must all derive from it.** Fancy art never substitutes for learning behavior; a functioning slider never excuses a visibly inaccurate object.

## 1. Invocation, scope, and interviewing

**Use** this skill for interactive explanations of mechanisms, algorithms, mathematical relationships, processes, assemblies, spatial structure, and inspection tasks. Typical figures: sampling and probability; tokens; embeddings; attention; model architecture; training curves; context windows; retrieval/chunking; validators; builders; process flow; agents; thresholds; trade-off frontiers; weighted scores; financial flow; caches; gear ratios; spring movement; sections; joints; object assembly; waves; launch phases; layered garments; interactive computation.

**Do not turn** a static infographic, dashboard, plain chart, decorative poster, slide deck, whole curriculum, or mere image request into a teaching simulation without user intent. You can produce the one causal mechanism embedded in a larger request, but don't claim to solve the larger artifact. These are **teaching models**, not certified clinical/engineering calculations, physical fabrication CAD, validated anatomy, historical truth, or measured improvements in learning.

**Guided mode, default:** only when essential design choices remain open, ask **one compact round**, up to five questions, supplying defaults: (1) learner/prior knowledge/language, (2) claim and misconception, (3) output target (standalone offline HTML by default or existing app), (4) fidelity/renderer (simplest faithful option), (5) 2–4 useful controls and one readout. Then state a concise plan and build after confirmation. Don't ask for facts visible in the source or project.

**Direct mode:** when the user says “build it,” “skip questions,” “直接生成,” or supplies adequate context, skip interviewing. Default to one standalone offline HTML file, the request's language, a small deterministic model, one question, 2–4 controls, reset, and the simplest renderer that meets the stated fidelity. State assumptions at delivery; don't stall for cosmetic preferences.

**If in an existing app:** inspect the actual component types, existing lesson schema, registry, style tokens, i18n, build system, and tests. Preserve them. Don't invent a top-level field or `kind` registration merely because this document mentions one. Where the host has its own prediction/explanation frame, insert **only the stage**—never nest a duplicate lesson frame.

## 2. Mechanism first: write a causal design note

Fill this out before drawing:

```text
CLAIM/ANCHOR: Exact lesson sentence or existing text segment that this figure explains.
LEARNER: Audience, prerequisite, study language, one observable learning goal.
CAUSE: A variable the learner can change; units and legal bounds.
EFFECT: The result that changes; its definition, units and visible evidence.
MISCONCEPTION: A plausible wrong prediction, specific enough to disprove.
TRUTH: Equation, state transition, logical rule, or valid sourced structure.
ASSUMPTIONS: What is cited, calculated, illustrative, omitted, or unknown.
MEANINGFUL TRY: The action and observation sufficient to challenge the misconception.
EXPLANATION: Two causal facts a good explanation must contain.
WRONG EXPLANATION: One tempting alternative that misses the mechanism.
TRANSFER: One new case the learner can predict without manipulating the demo.
OBJECT: Visual landmarks, materials, relationships, and source/asset rights.
MODEL KIND: Teaching pattern (e.g. kinematics, sampler, assembly).
RENDERER: 2D SVG, iso-lite, crafted mechanical SVG, 3D mesh/WebGL, field layer.
VIEW STATE: Camera, selection, labels, explosion and cutaway—not semantic state.
ACCEPTANCE: Numeric outcomes and visual states to check at reset/endpoints/transitions.
```

Use a **single source of truth**, conceptually:

```js
const initialState = Object.freeze({ /* semantic state only */ });
function applyAction(state, action) { /* validated pure transition; return new state */ }
function step(state, dt) { /* optional time-based update; deterministic */ }
function derive(state) { /* numbers, semantic labels, pose targets, aria summary */ }
function draw(derived, view) { /* SVG/Canvas/WebGL DOM changes; never alters truth */ }
```

Separate three different numerical claims: **source facts** (with source), **calculated outputs** (state equation and units), **illustrative parameters** (clearly designated). Clamp invalid inputs; reject impossible states rather than rendering plausible nonsense. State the limitations beside the figure.

**Teaching sequence:** question → prediction including misconception → meaningful experiment → accessible observation → causal explanation → transfer question → optional caveat. Allow the host's normal skip/reveal workflow if one exists; don't impose unexplained locks. First comparison changes **one cause**. Disclose unfamiliar terms adjacent to readouts. Favor 2–4 primary controls and a first useful discovery in under a minute; advanced inspection may be revealed progressively.

**Stochastic models:** show expected probabilities **separately** from sampled counts. Keep scores and other settings fixed in comparisons, expose a reproducible seed/reset if simulating, and never use one random draw as proof of a universal claim. If a misconception is too vague to falsify (“smarter”), translate it into a measurable quantity (“candidate selection share”) or state that the stronger claim cannot be tested here.

**Structural subjects:** a cutaway, rotation, occlusion test, part selection, or assembly ordering may itself be the meaningful action. Do not invent fictitious physical causality when inspection alone is the lesson.

### Portable conceptual teaching spec

This is **not** an instruction to alter an existing app's schema:

```js
const figureSpec = {
  id: "gear-ratio",
  anchor: "A large driven gear turns less for the same input rotation.",
  title: "Does adding an idler make the output slower?",
  lead: "Predict, turn the input one revolution, then compare output turns.",
  predict: ["It slows down", "It changes only direction", "It speeds up"],
  answer: 1,  // zero-based index for this standalone example
  config: { driverTeeth: 12, drivenTeeth: 24, inputTurns: 1 },
  insight: "The idler changes rotation direction; the first and last tooth counts determine magnitude.",
  caveat: "Ideal external gears: no slip, backlash, friction, or tooth-contact physics.",
  frame: "The output turns ___ times per input turn because ___."
};
```

## 3. Mechanism and renderer are DIFFERENT decisions

**Mechanism vocabulary:** `sampler`, `tokens`, `embedding`, `attention`, `architecture`, `training`, `context`, `chunker`, `validator`, `builder`, `flow`, `agent`, `threshold`, `frontier`, `scorer`, `economics`, `assembly`, `kinematics`, `section`, `spatial`. These are **planning labels**, not guaranteed app widgets, runtime classes, or registered types.

| Renderer | Use when | Must show | Must not claim |
|---|---|---|---|
| **2D SVG/DOM** | Curves, algorithms, sections, computation, processes | True relationships, labels and values | “3D” merely because of shading |
| **Iso-lite SVG** | Keys, calculator, synthesizer, switch, drawer, box-like control | One face-coordinate projection, working actions, output | Rigid-body physics |
| **Crafted mechanical SVG** | Pipes, frames, bearings, gears, cutaway, exploded object | Connected contacts, shafts, material, walls, credible part order | Tooth collision or mechanically verified assembly without a solver |
| **True 3D mesh (WebGL)** | Rotation/occlusion and perspective are part of teaching | World-space vertices, depth test, normals, orbit/pose and useful accessibility | Anatomical validity/physical simulation solely from rasterization |
| **Reference mesh** | Human organs, irregular biology, high-fidelity artifact | Licensed/cited subject-specific geometry and aligned transforms | That generic cubes/spheres are realistic anatomy |
| **Layered textile SVG/mesh** | Drapes, robes, folds, hems and overlaps | Silhouette, folded surfaces, thickness and shaded valleys | A flat trapezoid with stripes is accurate cloth |
| **WebGL field or Canvas overlay** | Heat, fluid, waves, light distribution | Model-driven field, clipping, fallback, continuous transitions | A shader is a validated CFD/optics solver |

Select **subject representation before renderer**. Record recognizable landmarks, silhouette, curvature and structural/material cues. A user who asks for simple schematic should not receive gratuitous complex machinery. A request for realistic human chest does **not** become fulfilled by generic smooth ellipsoids; if matching reference geometry isn't available, say the output is schematic and the realism request remains unmet. Source mesh data may be included **inside the generated HTML** when available/legally usable; no sibling `.json`, textures, fonts, imports or CDNs are permitted for standalone output.

## 4. Standalone output contract (no toolchain)

A standalone deliverable is **one `.html` file with inline `<style>`, `<svg>`/`<canvas>`, and inline `<script>`**, not an HTML page dependent on this Markdown document at runtime. It must work from `file://` when possible. Built-in browser APIs (DOM, SVG, Canvas, WebGL2, requestAnimationFrame, ResizeObserver, IntersectionObserver, Pointer Events, Web Audio after gesture) are allowed. Never require the old repository, scripts, copied kit, NPM, bundler, remote URL, API, image file or network for operation. A generated figure can of course be delivered as a new file; `FIGURES.md` is the **sole skill file**.

For React/Vite/Next or another host, split code **only if the host requires it**; this skill remains single-file. Keep browser-safe source separate from Node-only build logic, avoid window access during SSR, render the same resting pose on server and first client render, clean up listeners and RAFs. A Vite React project does not automatically have server components. When using server-built JSX as client props in Next.js, keys on root element props prevent deferred-element key warnings; prefer ordinary data-oriented payloads for large static geometry.

## 5. Reference implementation: complete working gear-ratio lesson

**Use the following as a copy-and-adapt pattern for any standalone figure.** It includes a single truth model, correct two-gear/idler arithmetic, prediction, two comparable trials, controls, semantic result, reset, transfer, narrow layout, focus-safe shortcuts and reduced motion. It is fully embedded: no stylesheet or JS imports. **This example is a schematic gear experiment**, not a physical tooth-mesh simulator.

```html
<!doctype html>
<html lang="en"><meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1">
<title>One gear, two experiments</title>
<style>
:root{color-scheme:light dark;font:16px/1.5 system-ui,sans-serif;--ink:light-dark(#202833,#e8edf3);--paper:light-dark(#fff,#171e27);--muted:light-dark(#526072,#b2bdca);--line:light-dark(#c7d1dd,#475363);--accent:light-dark(#254db3,#9bb5ff)}
body{background:var(--paper);color:var(--ink);margin:0;padding:clamp(12px,4vw,32px)}
main{max-width:940px;margin:auto}h1{font-size:clamp(1.35rem,3vw,2rem);line-height:1.2}p{max-width:68ch}
.lesson{display:grid;grid-template-columns:minmax(0,1.3fr) minmax(260px,1fr);gap:22px}
@media(max-width:690px){.lesson{grid-template-columns:1fr}}
.stage{min-width:0;border:1px solid var(--line);border-radius:14px;padding:12px;background:var(--paper)}
svg{display:block;width:100%;height:auto;max-height:350px;overflow:visible}
button,select{font:inherit;color:inherit;background:var(--paper);border:1px solid var(--line);border-radius:8px;padding:8px 11px;cursor:pointer}
button:focus-visible,select:focus-visible,input:focus-visible{outline:3px solid var(--accent);outline-offset:2px}
button[aria-pressed="true"]{background:var(--accent);color:var(--paper)}
.controls{display:flex;gap:8px;flex-wrap:wrap;align-items:center;margin:12px 0}
fieldset{border:1px solid var(--line);border-radius:10px;margin:8px 0 16px;padding:12px}
label{display:block;margin:7px 0}output{font-variant-numeric:tabular-nums;font-weight:650}
.small{color:var(--muted);font-size:.9rem}.readout{border-top:1px solid var(--line);padding-top:12px}
#answer,#insight{margin:12px 0}#answer[hidden],#insight[hidden]{display:none}
@media(prefers-reduced-motion:reduce){*{scroll-behavior:auto!important;transition:none!important;animation:none!important}}
</style>
<main>
<h1>Does an extra gear make the output slower?</h1>
<p>Claim: in an ideal external gear train, the first and last tooth counts determine the <em>magnitude</em> of the speed ratio.</p>
<div class="lesson"><section class="stage" aria-label="Gear mechanism and output turns">
<svg id="viz" viewBox="0 0 600 315" role="img" aria-labelledby="vtitle vdesc">
<title id="vtitle">Driver, optional idler and driven gear</title>
<desc id="vdesc">The tooth counts control output turns; the idler changes direction, not speed magnitude.</desc>
<g id="gearArt"></g></svg>
<p class="small">Schematic circles: tooth counts and turn direction are exact for the stated ideal gear model, but teeth and contact are not simulated.</p>
<div class="readout"><output id="result" aria-live="polite">Input +1.00 turn → output −0.50 turns</output></div>
<p id="trial" class="small">Complete trials with and without the idler to compare their outcomes.</p>
</section><section>
<h2>1. Predict</h2><fieldset><legend>What does an idler gear change?</legend>
<label><input name="prediction" type="radio" value="slow"> It makes the output turn less.</label>
<label><input name="prediction" type="radio" value="direction"> It reverses the output direction but not the ratio magnitude.</label>
<label><input name="prediction" type="radio" value="fast"> It makes the output turn more.</label></fieldset>
<h2>2. Try</h2><label for="teeth">Driven gear tooth count</label>
<select id="teeth"><option value="12">12</option><option selected value="24">24</option><option value="36">36</option></select>
<div class="controls"><label><input id="idler" type="checkbox"> Add an idler</label></div>
<div class="controls"><button id="turn" type="button">Turn input +1 revolution</button><button id="reset" type="button">Reset</button></div>
<p class="small">Do two trials: same tooth counts, idler off then on. You can change the output gear afterward.</p>
<button id="reveal" type="button">Show the explanation</button>
<div id="insight" hidden><h2>3. Explain</h2><p>An external contact reverses rotation direction. With no idler, there is one reversal; adding an idler creates two. For ideal gears, the intermediate tooth count cancels, so magnitude is always driver teeth ÷ driven teeth.</p><p><strong>Transfer:</strong> With 12 driving teeth and 36 driven teeth, what output magnitude follows one input turn? <button id="transfer" type="button">Check</button> <output id="transferResult"></output></p></div>
<p class="small">Model limits: no slip, backlash, losses, actual tooth engagement or gear manufacturing constraints. Values are illustrative ideal ratios.</p>
</section></div></main>
<script>
(()=>{'use strict';
const $=s=>document.querySelector(s),s={driver:12,driven:24,idler:false,turns:0},view={phase:0};
const completed={without:null,with:null};const NS='http://www.w3.org/2000/svg';
const ratio=()=>s.driver/s.driven;
const result=()=>s.turns*ratio()*(s.idler?1:-1); // two external contacts with idler, one without
const el=(tag,attrs={},text)=>{let n=document.createElementNS(NS,tag);for(const [k,v]of Object.entries(attrs))n.setAttribute(k,String(v));if(text!==undefined)n.textContent=text;return n};
function wheel(g,x,y,r,angle,teeth,name,accent){
 const group=el('g',{transform:`translate(${x} ${y}) rotate(${angle})`});
 group.append(el('circle',{r,fill:accent?'#9bb5ff':'#b7c9d5',stroke:'#435a73','stroke-width':3}));
 for(let i=0;i<12;i++){let a=i*30*Math.PI/180;group.append(el('rect',{x:-5,y:-r-7,width:10,height:14,rx:2,fill:'#435a73',transform:`rotate(${i*30})`}))}
 group.append(el('circle',{r:8,fill:'#435a73'}),el('line',{x1:0,y1:0,x2:r-11,y2:0,stroke:'#fff','stroke-width':4,'stroke-linecap':'round'}));
 g.append(group,el('text',{x,y:y+r+28,'text-anchor':'middle','font-size':16,fill:'currentColor'},`${name} · ${teeth} teeth`));
}
function paint(){
 const art=$('#gearArt');art.replaceChildren();
 // Separate display positions from mechanical ratio. The circles are teaching symbols.
 wheel(art,100,140,57,view.phase*360,12,'Input',true);
 if(s.idler)wheel(art,285,140,47,-view.phase*360*12/18,18,'Idler',false);
 wheel(art,475,140,72,view.phase*360*(s.idler?1:-1)*ratio(),s.driven,'Output',false);
 $('#result').textContent=`Input ${s.turns>=0?'+':''}${s.turns.toFixed(2)} turn${s.turns===1?'':'s'} → output ${result()>=0?'+':''}${result().toFixed(2)} turns (${result()<0?'opposite':result()>0?'same':'no'} direction)`;
 const a=completed.without,b=completed.with;
 $('#trial').textContent=a!==null&&b!==null?`Trial comparison (same ${s.driven} driven teeth): no idler ${a.toFixed(2)}, with idler ${b.toFixed(2)}. Magnitudes are equal; directions differ.`:'Complete one input turn with idler off, then reset turns and complete another with idler on.';
}
function commit(){const r=result();if(Math.abs(s.turns-1)<1e-8)completed[s.idler?'with':'without']=r;paint()}
$('#turn').addEventListener('click',()=>{s.turns+=1;view.phase=s.turns;commit()});
$('#teeth').addEventListener('change',e=>{s.driven=Number(e.target.value);s.turns=0;view.phase=0;completed.without=null;completed.with=null;paint()});
$('#idler').addEventListener('change',e=>{s.idler=e.target.checked;s.turns=0;view.phase=0;paint()});
$('#reset').addEventListener('click',()=>{s.driven=24;s.idler=false;s.turns=0;view.phase=0;completed.without=null;completed.with=null;$('#teeth').value='24';$('#idler').checked=false;$('#insight').hidden=true;$('#reveal').textContent='Show the explanation';$('#transferResult').textContent='';document.querySelectorAll('input[name=prediction]').forEach(e=>e.checked=false);paint()});
$('#reveal').addEventListener('click',()=>{$('#insight').hidden=false;const p=document.querySelector('input[name=prediction]:checked');$('#reveal').textContent=p&&p.value==='direction'?'Prediction matches the ideal model':'Review the observed directions and ratio';});
$('#transfer').addEventListener('click',()=>{$('#transferResult').textContent='1/3 turn in magnitude (direction depends on number of contacts).'});
paint();
})();
</script></html>
```

**Example acceptance arithmetic**: driver 12 / driven 24, +1 input turn → −0.5 output turn without idler; +0.5 with one idler. Driver 12 / driven 36 → magnitude 1/3 turn. The schematic wheels are not drawn at a physically meaningful center distance and should not be misrepresented as meshed gears. For a literal gear inspection, use the mechanical construction rules below to align pitch circles and model real contact.

## 6. Inline isometric SVG toolkit (functional devices)

When a lesson needs a working calculator, synth, display, button, switch, slider or drawer, use **one face-local coordinate system** and make the output real. An isometric drawing is a presentation of the mechanism, not the mechanism itself.

The following compact kernel is adapted from **iso-figure, MIT © 2026 Tolga Cohce** (full notice below). **Paste it into the generated page's existing inline script; do not import it from any file.** It provides points, surface matrices and simple boxes. In a generated figure, choose a camera offset after projecting the entire scene's extrema.

```js
// ISO-LITE: no packages, modules or files
const ISO = (() => {
  const C=Math.cos(Math.PI/6), S=Math.sin(Math.PI/6);
  let ox=400,oy=240,scale=1;
  function fit({x=400,y=240,zoom=1}={}){ox=x;oy=y;scale=zoom}
  const P=(x,y,z)=>[(x-y)*C*scale+ox,(x+y)*S*scale-z*scale+oy];
  const D=(x,y,z)=>[(x-y)*C*scale,(x+y)*S*scale-z*scale];
  const plane=(origin,U,V)=>{const o=P(...origin),u=D(...U),v=D(...V);
    return `matrix(${u[0]} ${u[1]} ${v[0]} ${v[1]} ${o[0]} ${o[1]})`};
  const top=(x,y,z)=>plane([x,y,z],[1,0,0],[0,1,0]);
  const front=(x,y,z)=>plane([x,y,z],[1,0,0],[0,0,-1]);
  const side=(x,y,z)=>plane([x,y,z],[0,-1,0],[0,0,-1]);
  function rect(t,w,h,r=0,fill='var(--face,#bec8d6)') {
    const n=document.createElementNS('http://www.w3.org/2000/svg','g');
    n.setAttribute('transform',t);const q=document.createElementNS(n.namespaceURI,'rect');
    for(const [k,v] of Object.entries({width:w,height:h,rx:r,fill}))q.setAttribute(k,String(v));
    n.append(q);return n;
  }
  function box(x,y,z,w,d,h,rounded=0) {
    const n=document.createElementNS('http://www.w3.org/2000/svg','g');
    n.append(
      rect(side(x+w,y+d,z+h),d,h,Math.min(rounded,h/4),'var(--side,#6d8199)'),
      rect(front(x,y+d,z+h),w,h,Math.min(rounded,h/4),'var(--front,#8ba0b8)'),
      rect(top(x,y,z+h),w,d,rounded,'var(--top,#d9e3ee)'));
    return n;
  }
  return {fit,P,D,plane,top,front,side,rect,box};
})();
```

**Coordinate convention:** +x down-right, +y down-left, +z up; front face is at `y=max`, right side at `x=max`. A top-face key at local `(u,v)` can be rendered as an ordinary `<rect>` in `<g transform="${ISO.top(x,y,z)}">…</g>`; put its label in that same group. The `box()` helper assumes simple non-intersecting objects, with side, front and top faces drawn in that order. It is **not a general occlusion or physics engine**. Split occluding parts into front/back, or use measured 3D geometry as complexity grows. Never mix the kernel's coordinate system with a differently defined camera without converting positions.

**Working device recipe:**

1. Define state and its real algorithm: calculator input/output; synth note set, frequency and waveform; switch state; drawer position and contents. Validate legal input.
2. Use `ISO.box()` for main enclosure and face-local `<g>` layers for buttons, display text, knobs and labels. Group movable art with the part it belongs to.
3. All input paths call **the same action**: SVG pointer, native buttons/sliders, keyboard shortcut (only when the figure has focus). The SVG is optional; native equivalents are mandatory when it has clickable parts.
4. Render the actual semantic output: computed digits, oscillator mode, a signal trace, chosen content or state label. Never pretend that a pressed animation is an effect of the model.
5. Reset everything. For held keys, track input ownership by pointer/keyboard source; `pointercancel`, `pointerup`, window blur, visibility changes and reset must release notes/presses. Initiate Web Audio **only after a user gesture** and offer mute plus a visible non-audio result.
6. Project the extreme corners for each legal state, fit a margin, prevent clipped labels, and use opaque fills with `vector-effect="non-scaling-stroke"` for detail strokes.

**CSS themes and accessibility:** scope tokens to the figure (`.figure { --top:... }`), never override the page's `:root` from an embedded widget. Provide `aria-label` or a nearby textual result that describes the output, native controls with names/values, a focus ring, keyboard route, and reset. Disable decorative key transitions under reduced motion but keep computed behavior live.

## 7. Inline mechanical projection and primitive recipe

For a mechanism involving **supports, axles, joints, pipe connections, cutaway walls, springs or an explosion**, a believable assembly needs actual part relationships before detail. Build in world coordinates: **x/y along the floor, z upward**. Use one camera for every part. The following browser-only helper makes shallow solid extrusions from polygon profiles and camera-sorted faces (no external `iso-kit`, lathe, tube or recorder required).

```js
// CRAFT: SVG mechanical extrusion helper (paste inside generated page's inline JS)
const CRAFT=(()=>{
  const NS='http://www.w3.org/2000/svg';
  const camera={az:Math.PI/4,elev:Math.PI/6,s:2,cx:400,cy:260};
  function project(p){const [x,y,z]=p,a=camera.az,e=camera.elev;
    const h=Math.cos(a)*x-Math.sin(a)*y;
    const v=Math.sin(a)*x+Math.cos(a)*y;
    return [camera.cx+camera.s*h,camera.cy+camera.s*(Math.sin(e)*v-Math.cos(e)*z)];}
  function depth(p){const [x,y,z]=p,a=camera.az,e=camera.elev;
    return (Math.sin(a)*x+Math.cos(a)*y)*Math.cos(e)+z*Math.sin(e);}
  function face(corners,fill,part){return {corners,fill,part,depth:corners.reduce((n,p)=>n+depth(p),0)/corners.length};}
  function extrusion(profile,z0,z1,material={},part='solid'){
    if(profile.length<3||!(z1>z0))throw Error('Need >=3 profile points and positive thickness');
    const bot=profile.map(([x,y])=>[x,y,z0]),top=profile.map(([x,y])=>[x,y,z1]);
    const fs=[face([...bot].reverse(),material.bottom||'#667a8d',part),face(top,material.top||'#d5dfeb',part)];
    for(let i=0;i<profile.length;i++){
      const j=(i+1)%profile.length;
      fs.push(face([bot[i],bot[j],top[j],top[i]],material.side||'#8ba0b4',part));
    }return fs;
  }
  function disc(cx,cy,r,z0,z1,n=32,material={},part='disc'){
    const points=Array.from({length:n},(_,i)=>{const a=i*2*Math.PI/n;return [cx+r*Math.cos(a),cy+r*Math.sin(a)]});
    return extrusion(points,z0,z1,material,part);
  }
  function draw(svg,faces){
    // Exact depth for independent face centroids, not a complete polygon-intersection solution.
    for(const f of [...faces].sort((a,b)=>a.depth-b.depth)){
      const n=document.createElementNS(NS,'polygon');
      n.setAttribute('points',f.corners.map(p=>project(p).map(v=>v.toFixed(2)).join(',')).join(' '));
      n.setAttribute('fill',f.fill);n.setAttribute('stroke','var(--edge,#43556a)');
      n.setAttribute('stroke-width','.9');n.setAttribute('vector-effect','non-scaling-stroke');
      n.dataset.part=f.part;svg.append(n);
    }
  }
  return {camera,project,depth,face,extrusion,disc,draw};
})();
// Example use: CRAFT.draw(stage,[
//   ...CRAFT.extrusion([[-70,-45],[70,-45],[70,45],[-70,45]],0,8,{},'base'),
//   ...CRAFT.disc(0,0,25,8,24,48,{},'hub')
// ]);
```

**Important limits:** painter sorting by face-center is reliable only for suitably separated/convex parts. Intersecting, wrapped, strongly concave or cyclically occluding faces can still draw incorrectly. Split complex surfaces, sort actual fragments, or use the **WebGL depth-tested path in §8**. Do not treat this helper as a drop-in equivalent of an industrial geometry or collision kernel.

**Mechanical craft rules:**

- Start with a **parts plan**: fixed base/support; input/control; transmission; output/readout; interior/cover; movable and selectable groups. Record each part's position, units, purpose, mounting face/axis, range, and material. The design should be recognizable from silhouette and a few key details before adding micro-detail.
- For round devices construct 24–64-vertex discs; a chamber/nozzle uses a stack of rings with varying radii (lathed profile) and real inner/outer surfaces where sectioned; a tube follows a series of centerline points and should have visible connections at endpoints. For shafts/bearings keep axes coaxial; for a spring show its seat connections; for support plates ensure visible contact. Use proper metal/rubber/ceramic/fabric shading rather than decorative noise.
- For a cutaway, draw **actual wall thickness, exposed section surfaces, rim and inside**. Removing only the front cover is an inspection action, not a physical cut. Clip any internal field/display to both its own face and the opening.
- An exploded assembly uses **per-group rest transform + physically plausible travel vector + bounds + stagger order**. Outside pieces clear before interior pieces; closing reverses. When reversing mid-explosion, cancel pending moves and reschedule only groups with changed target. Returning to the rest pose must restore all exact rest transforms. An exploded view is inspection; pause the simulated machine while parts are apart or label it as a pose visualization.
- A software algorithm represented as a machine must describe its **analogy mapping**; a mechanism represented as a real machine must respect actual dimensions/constraints. **An idler's tooth count cancels from ideal gear magnitude**; a visual gear does not magically compute meshing.
- Use one shared `derive()` to drive group transforms, numbers, selected-part readout, textual observation and captions. Precompute unchanging geometry. Move rigid parts with transforms; rebuild only shape-changing parts.
- Check assembled, halfway, disassembled, and reversed-motion states. Zoom in on contacts, pipes and cut boundaries. If any part floats, passes through a neighbor, lacks a port or merges visually, change geometry or assembly sequencing rather than hiding the flaw with painter-order hacks.

**Geometry at a glance:** for two external gears with pitch radii `r1, r2`, ideal center distance is `r1+r2`; angular velocities obey `ω2 = −ω1*r1/r2`, and for equal module tooth counts give `ω2 = −ω1*N1/N2`. Two successive external contacts yield positive output direction. For a *linear static* spring, `x = F/k` only in its stated elastic regime. For a slider, clamp travel to the real end stops. Do not apply these formulas outside their assumptions.

## 8. Inline true-3D mesh viewer (WebGL2, no libraries)

Use a world-space mesh with depth-tested triangles when viewing angle and occlusion actually teach the subject. **Do not call a rotating 2D image true 3D.** The following small **self-contained** viewer supports **arbitrary inlined triangular geometry**, computed face normals, world orbit controls, resize handling and cleanup. The generated HTML must embed or programmatically generate `positions` and `triangles`; do not load a sibling OBJ/GLB/JSON. It gives **flat-shaded triangle rendering**, not medical realism or a physics engine. Display control values and a textual pose/part description alongside it.

```js
// MESH VIEWER: call createMeshViewer(canvas, positions, triangles) from an inline script.
// positions: flat [x,y,z,...]. triangles: flat integer indices [a,b,c,...].
function createMeshViewer(canvas,positions,triangles){
  const gl=canvas.getContext('webgl2',{antialias:true,alpha:false});
  if(!gl)return null; // caller must show an informative SVG/text alternative
  if(positions.length%3||triangles.length%3||!triangles.length)throw Error('Invalid mesh');
  if(!positions.every(Number.isFinite))throw Error('Non-finite vertex');
  const vertexCount=positions.length/3;
  if(!triangles.every(i=>Number.isInteger(i)&&i>=0&&i<vertexCount))throw Error('Bad mesh index');
  let lo=[Infinity,Infinity,Infinity],hi=[-Infinity,-Infinity,-Infinity];
  for(let i=0;i<positions.length;i+=3)for(let k=0;k<3;k++){
    lo[k]=Math.min(lo[k],positions[i+k]);hi[k]=Math.max(hi[k],positions[i+k]);}
  const mid=lo.map((v,k)=>(v+hi[k])/2),span=Math.max(...lo.map((v,k)=>hi[k]-v));
  if(!(span>0))throw Error('Mesh has no extent');
  const p=[];for(let i=0;i<positions.length;i+=3)
    p.push(...mid.map((v,k)=>(positions[i+k]-v)*2/span));
  const out=[];
  const sub=(a,b)=>a.map((v,i)=>v-b[i]);
  const cross=(a,b)=>[a[1]*b[2]-a[2]*b[1],a[2]*b[0]-a[0]*b[2],a[0]*b[1]-a[1]*b[0]];
  const vec=i=>p.slice(i*3,i*3+3);
  for(let i=0;i<triangles.length;i+=3){
    const A=vec(triangles[i]),B=vec(triangles[i+1]),C=vec(triangles[i+2]);
    let n=cross(sub(B,A),sub(C,A));const d=Math.hypot(...n);if(d<1e-12)continue;n=n.map(v=>v/d);
    for(const a of [A,B,C])out.push(...a,...n);
  }
  const vs=`#version 300 es
  precision highp float;
  in vec3 aPosition; in vec3 aNormal;
  uniform float uYaw,uPitch,uAspect;
  out vec3 vNormal;
  void main(){
    float cy=cos(uYaw),sy=sin(uYaw),cx=cos(uPitch),sx=sin(uPitch);
    mat3 ry=mat3(cy,0.,-sy, 0.,1.,0., sy,0.,cy);
    mat3 rx=mat3(1.,0.,0., 0.,cx,sx, 0.,-sx,cx);
    mat3 R=rx*ry;vec3 q=R*aPosition;
    vNormal=normalize(R*aNormal);
    float distance=4.5,near=0.1,far=30.,f=2.41421356; // 45° FOV
    float viewZ=q.z-distance;
    gl_Position=vec4(q.x*f/uAspect,q.y*f,
      (far+near)/(near-far)*viewZ+(2.*far*near)/(near-far),-viewZ);
  }`;
  const fs=`#version 300 es
  precision highp float;in vec3 vNormal;out vec4 color;
  void main(){vec3 light=normalize(vec3(.5,.9,1.));
    float value=.36+.64*abs(dot(normalize(vNormal),light));
    color=vec4(vec3(.38,.59,.80)*value,1.);
  }`;
  function shader(type,src){const s=gl.createShader(type);gl.shaderSource(s,src);gl.compileShader(s);
    if(!gl.getShaderParameter(s,gl.COMPILE_STATUS))throw Error(gl.getShaderInfoLog(s));return s;}
  const prog=gl.createProgram();gl.attachShader(prog,shader(gl.VERTEX_SHADER,vs));
  gl.attachShader(prog,shader(gl.FRAGMENT_SHADER,fs));gl.linkProgram(prog);
  if(!gl.getProgramParameter(prog,gl.LINK_STATUS))throw Error(gl.getProgramInfoLog(prog));
  const buffer=gl.createBuffer(),vao=gl.createVertexArray();gl.bindVertexArray(vao);
  gl.bindBuffer(gl.ARRAY_BUFFER,buffer);gl.bufferData(gl.ARRAY_BUFFER,new Float32Array(out),gl.STATIC_DRAW);
  gl.useProgram(prog);const stride=6*4;
  for(const [name,offset] of [['aPosition',0],['aNormal',12]]){
    const at=gl.getAttribLocation(prog,name);gl.enableVertexAttribArray(at);
    gl.vertexAttribPointer(at,3,gl.FLOAT,false,stride,offset);}
  const uy=gl.getUniformLocation(prog,'uYaw'),up=gl.getUniformLocation(prog,'uPitch'),
        ua=gl.getUniformLocation(prog,'uAspect');
  gl.enable(gl.DEPTH_TEST);gl.disable(gl.CULL_FACE);
  let yaw=.55,pitch=.28,disposed=false,visible=true,raf=0;
  function draw(){raf=0;if(disposed||!visible)return;
    const w=Math.max(1,Math.round(canvas.clientWidth*Math.min(devicePixelRatio||1,2)));
    const h=Math.max(1,Math.round(canvas.clientHeight*Math.min(devicePixelRatio||1,2)));
    if(canvas.width!==w||canvas.height!==h){canvas.width=w;canvas.height=h;}
    gl.viewport(0,0,w,h);gl.clearColor(.065,.09,.13,1);gl.clear(gl.COLOR_BUFFER_BIT|gl.DEPTH_BUFFER_BIT);
    gl.useProgram(prog);gl.bindVertexArray(vao);gl.uniform1f(uy,yaw);gl.uniform1f(up,pitch);
    gl.uniform1f(ua,w/h);gl.drawArrays(gl.TRIANGLES,0,out.length/6);
  }
  const request=()=>{if(!raf&&!disposed&&visible)raf=requestAnimationFrame(draw)};
  function setOrbit(a,b){yaw=a;pitch=Math.max(-1.4,Math.min(1.4,b));request();}
  let start=null;
  const down=e=>{if(e.button!==0)return;start=[e.pointerId,e.clientX,e.clientY,yaw,pitch];canvas.setPointerCapture(e.pointerId);};
  const move=e=>{if(!start||start[0]!==e.pointerId)return;
    setOrbit(start[3]+(e.clientX-start[1])*.008,start[4]+(e.clientY-start[2])*.008);};
  const end=e=>{if(start&&start[0]===e.pointerId)start=null;};
  canvas.style.touchAction='none';canvas.addEventListener('pointerdown',down);
  canvas.addEventListener('pointermove',move);canvas.addEventListener('pointerup',end);
  canvas.addEventListener('pointercancel',end);
  const observer=typeof IntersectionObserver==='function'?new IntersectionObserver(entries=>{
    visible=entries[entries.length-1].isIntersecting;if(visible)request();
  },{rootMargin:'120px 0px'}):null;
  observer?.observe(canvas);
  const resize=typeof ResizeObserver==='function'?new ResizeObserver(request):null;resize?.observe(canvas);
  request();
  return {setOrbit,home(){setOrbit(.55,.28)},dispose(){disposed=true;cancelAnimationFrame(raf);
    observer?.disconnect();resize?.disconnect();canvas.removeEventListener('pointerdown',down);
    canvas.removeEventListener('pointermove',move);canvas.removeEventListener('pointerup',end);
    canvas.removeEventListener('pointercancel',end);gl.deleteBuffer(buffer);gl.deleteVertexArray(vao);
    gl.deleteProgram(prog);}};
}
```

**Wiring example** (all inline in the generated page):

```html
<canvas id="mesh" width="600" height="350" style="width:100%;height:350px" aria-label="Rotatable model; use angle sliders for keyboard access"></canvas>
<label>Azimuth <input id="yaw" type="range" min="-180" max="180" value="30"></label>
<label>Elevation <input id="pitch" type="range" min="-75" max="75" value="20"></label>
<button id="home">Home view</button><p id="meshStatus" role="status"></p>
<script>
// Paste createMeshViewer here. The following is an inlined six-face cube, demonstrative only.
const positions=[-1,-1,-1,1,-1,-1,1,1,-1,-1,1,-1,-1,-1,1,1,-1,1,1,1,1,-1,1,1];
const triangles=[0,2,1,0,3,2,4,5,6,4,6,7,0,1,5,0,5,4,2,3,7,2,7,6,1,2,6,1,6,5,0,4,7,0,7,3];
const viewer=createMeshViewer(document.querySelector('#mesh'),positions,triangles);
const yaw=document.querySelector('#yaw'),pitch=document.querySelector('#pitch'),status=document.querySelector('#meshStatus');
function show(){const a=Number(yaw.value),b=Number(pitch.value);viewer?.setOrbit(a*Math.PI/180,b*Math.PI/180);
  status.textContent=`View: azimuth ${a}°, elevation ${b}°. Geometry is schematic.`;}
yaw.addEventListener('input',show);pitch.addEventListener('input',show);
document.querySelector('#home').addEventListener('click',()=>{yaw.value=30;pitch.value=20;show()});
if(!viewer)status.textContent='WebGL2 is unavailable. Use a separate SVG diagram or textual part description.';else show();
</script>
```

**True 3D constraints:** vertices must describe the subject faithfully, with sufficient tessellation where curvature matters. Mesh visibility depends on valid triangles and winding. Normals above are flat normals; smooth curved materials need averaged or source-provided vertex normals, more capable lighting and actual domain geometry. Avoid assuming a convex-polyhedron renderer can represent organic bodies or cloth. When a part bends or hinges, update model-space vertices or apply hierarchical transforms with proper joint constraints; the static single-mesh viewer does not create bones/hinges for you. When source meshes are licensed, keep source, modifications, orientation, units and attribution together in the output's inline metadata or visible caption.

**Orbit is *view state*.** The learner's numerical answer cannot change merely by rotating the camera. Provide home, azimuth/elevation native inputs, selected-part descriptions, reset of semantic and view state as appropriate, and a stable reduced-motion stepped option. Avoid rendering continuously for a static scene: the viewer above renders only on change and while visible. Benchmark actual triangulation, pixel ratio, device and input responsiveness; do not promise any fixed FPS without measurement.

## 9. Field effects, time and motion without external engines

Use ordinary SVG for fields that a few paths can explain. Canvas 2D can draw a grid of mathematically calculated samples. WebGL2 fragment shaders are optional when they materially improve an *observable* wave, heat, light or fluid model—not for decorative glow. Distinguish a **scientific model** from its **art direction**. A valid visualization must reach zero at edges where clipping is required and must share the figure's coordinates with connected machinery.

**Self-contained Canvas2D scalar field** (no shader toolchain):

```js
function drawField(canvas,model,{nx=96,ny=54}={}){
  // model(x,y) returns a finite normalized scalar in [0,1].
  const ctx=canvas.getContext('2d');if(!ctx)return;
  const pixels=ctx.createImageData(nx,ny);
  for(let j=0;j<ny;j++)for(let i=0;i<nx;i++){
    const x=i/(nx-1),y=j/(ny-1),v=Math.max(0,Math.min(1,model(x,y)));
    const k=(j*nx+i)*4;
    pixels.data[k]=Math.round(25+135*v);
    pixels.data[k+1]=Math.round(42+155*v);
    pixels.data[k+2]=Math.round(65+170*v);
    pixels.data[k+3]=255;
  }
  const temp=document.createElement('canvas');temp.width=nx;temp.height=ny;
  temp.getContext('2d').putImageData(pixels,0,0);
  ctx.clearRect(0,0,canvas.width,canvas.height);
  ctx.imageSmoothingEnabled=true;ctx.drawImage(temp,0,0,canvas.width,canvas.height);
}
// Illustrative radial wave with smooth spatial envelope:
// const wave=(x,y,t)=>{const r=Math.hypot(x-.5,y-.5);const env=Math.max(0,1-r/.65);
//   return .5+.5*Math.sin(32*r-6*t)*env*env;};
```

For real wave interpretation, specify wavelength, phase, units, source configuration and boundary conditions; the commented ripple is **an illustrative pattern**, not a validated tank solver. If paired with a machine, layer the effect **behind** the necessary structural foreground (`back SVG → Canvas → front SVG`); keep canvas `pointer-events:none` unless it *is* an interaction surface. Mask both the field plane and the real object opening. Provide a native slider or stepped phase and a textual magnitude/readout under WebGL failure and reduced motion.

**Motion discipline:** `dt = min((timestamp−last)/1000, 1/30)`, not “one step per frame.” A simple display transition can follow `x += (target−x)*(1−exp(−dt/tau))`, with `tau≈0.1–0.3 s`. A static ideal spring example can use `x=F/k` as its *truth* and separate any easing as *display*. For a dynamical spring, integrate its governing law with small substeps (e.g. 1/240 s), specified mass, stiffness, damping, force and end stops. Easing is not automatically a physical model.

For critically damped *display* state `v += (ω²(target−x)−2ωv)dt; x += v dt`; substep, clamp, and stop when both position error and speed are small. Under reduced motion, set display equal to the target and offer step controls; the model still updates. For one-shot signals, store event start time and use an attack/decay envelope rather than one-frame flashes. For stopping a light/heat effect, avoid unwanted brief brightening at the moment of stop.

**Loop lifecycle:** at most one RAF per figure; start on meaningful input or active time simulation; stop when settled, paused, hidden, offscreen or unmounted. On `IntersectionObserver`, read `entries[entries.length-1]` (the newest entry). Cancel RAF, observers, audio, timeouts, held input and event listeners on cleanup. If using React, write fast-moving SVG attributes via refs rather than rerendering the entire page each animation frame. Do not autoplay the explanation before prediction; a demo must be deliberately requested or follow the host's learning sequence. Avoid idle-resume tours that start while the learner is thinking.

**Gesture isolation:** figure shortcuts apply only while focus is inside the figure, ignore text inputs, textareas, select menus and contenteditable regions, and never override Ctrl/Meta/Alt shortcuts. For SVG pointer drags, pointer capture only the draggable area; release on up/cancel/blur; provide a native equivalent. On browser blur or document hidden, release held actions and any sounds. When rendering on Canvas/WebGL, textual output must be DOM-accessible; a labeled canvas alone cannot communicate the new result to non-visual users.

## 10. Build recipes from the six core lesson families

The following reproduce **teaching mechanisms and content coverage**, not their original screenshots, proprietary art, source-specific meshes, or multi-megabyte inline geometry. Every recipe can be implemented from this single document without importing source files. Source-specific real-world fidelity still requires proper evidence or user-supplied domain data.

### 10.1 Conic section in space

**Question:** if a cone's radius grows linearly with distance from its tip, does cross-sectional area also grow linearly? **Model:** for cone height `H`, base radius `R`, coordinate `z` measured upward from tip: `r(z)=Rz/H`, `A(z)=π(Rz/H)²`, with `0≤z≤H`. At `z=H/2`, `r=R/2`, `A=πR²/4`. **Try:** move cut plane from `H/2` to `H` and compare area; orbit the true mesh to inspect the cut. **Visual:** sampled circumference rings, side triangles, section cap and consistent clipping; normals/occlusion matter in 3D. **Transfer:** doubling radius multiplies area by four. **Caveat:** exact geometry, illustrative dimensions; no material/physics claim.

### 10.2 Human chest inspection

**Question:** how do lungs, heart, airways and the diaphragm sit relative to each other and the chest wall? **Representation:** subject-specific licensed torso and organ meshes; retain a **common source coordinate system and scale**; record crop, simplification, recolor and source version. **Try:** rotate, open a chest window, select structures, and restore view. **Model:** structure and visibility, not claims about physiology. An independently drawn diaphragm can be labeled *schematic* and kept distinguishable from sourced anatomy. **Do not fabricate anatomically accurate shapes from spheres or scale each organ to the center independently.** If sourced meshes are not available, deliver only a clearly marked anatomical **schematic** and say that photorealistic/source-accurate chest functionality is not achieved. The upstream exemplar used **Human Reference Atlas / HuBMAP** meshes under **CC BY 4.0** with alterations and attribution; those mesh bytes are **not embedded in this skill**.

### 10.3 Rocket launch and cutaway

**Question:** when does thrust overcome weight, what happens at burnout, and how does coasting differ from powered ascent? **Model (illustrative one-dimensional vertical flight):** state `{time,altitude,velocity,fuel}`; `mass=dryMass+fuel`, `weight=mass*g`; if `fuel>0` and engine enabled, `thrust=T`, `fuel' = max(0,fuel−mdot*dt)`; on pad and not lifting, pad normal force `N=max(0,weight−thrust)`, net acceleration zero; after liftoff `a=(thrust−mass*g−drag)/mass` where `drag` is stated or zero. Integrate with fixed time steps (semi-implicit Euler for simple teaching) and stop/reconcile at fuel exhaustion and ground contact. **Do not show negative altitude or unphysical negative fuel.** Controls: thrust, fuel/phase, step/pause/reset, engine cross-section/explosion. **Readouts:** thrust/weight ratio, normal force, acceleration, height, velocity, fuel and phase. **Visual:** recognizable tank, feed plumbing, chamber, narrowing throat, expanding bell, supports and section wall; no generic rocket icon where engine detail is requested. **Caveat:** 1D ideal physics, no aerodynamic or engine-performance certification.

### 10.4 Transformer data flow

**Question:** which earlier token contributes to a chosen token after masked self-attention? **Toy model:** given input vectors `X`, compute `Q=XWq`, `K=XWk`, `V=XWv`; logits `L=QKᵀ/√d_k`; set future-token logits to negative infinity for a causal mask, `A=softmax(L)` per row, and output `O=AV`. Show residual addition, normalization and a simple feed-forward map if included in the taught block, not an invented model architecture. Display the same selected row's numeric weights as diagram thicknesses and readouts. Compare masked/unmasked or change one input element while holding weights fixed. **Caveat:** tiny illustrative vectors and fixed matrices; one head is not a full production language model. **Transfer:** what changes if a token has no access to future positions?

### 10.5 Museum garment, pleats and layered clothing

**Question:** which layers overlap, and how can a pleat increase available fabric width? **Representation:** begin with a *specific* documented garment, a source and distinguishable historical facts. Draw the garment's real silhouette, opening/overlap direction, hem and collar; layers become shaded *surfaces* with visible fold ridges and darker valleys; show a cross-section or peel-apart view if it teaches structure. **Interaction:** inspect fold/cross-section, select a panel, restore assembled pose. **Do not call a generic trapezoid with parallel lines historically authentic pleated cloth.** Separate source-backed construction from conjectural restoration; a rendering is not textile mechanics or provenance evidence.

### 10.6 Gear ratios and the idler

**Question:** does adding an idler change speed magnitude? **Model:** each external meshing contact reverses sign. For driver `N1` and output `N3`, `outputTurns = inputTurns*(−1)^contacts*(N1/N3)`; with one idler there are two contacts. Equal-size idler or variable idler tooth count does not alter final magnitude in the **ideal train**, but changes mechanical layout, and direction changes with parity. **Try:** same driver/output and one input turn, idler off/on. **Verify:** 12:24 → `−0.5` without idler and `+0.5` with idler. **Transfer:** with final 36 teeth, output magnitude is `1/3`, whichever idler tooth count fits the layout. **Caveat:** no real tooth contact, backlash or losses in the schematic. The complete offline code is in §5.

### Additional reusable lesson patterns

| Mechanism | Model rule | Observe, then transfer |
|---|---|---|
| **Sampling temperature** | `q_i(T)=exp(log(p_i)/T)/Σ_j exp(log(p_j)/T)`, positive `T`, fixed base probabilities. | Higher T spreads probability mass, not necessarily truth; show expected shares vs seeded sampled counts. If the top-scored candidate is false, concentrating on it does not make it correct. |
| **LRU cache** | Cache hit moves item to most recently used; full miss evicts least recently used. | Old-but-frequently-accessed entry survives, recently inserted-but-unused may leave. |
| **Backpressure/queue** | Finite-capacity arrivals, service and rejected items; e.g. `processed=min(queue+arrivals,serviceCapacity)`, `overflow=max(0,queue+arrivals−processed−capacity)`. | Faster arrivals create backlog/rejection instead of increasing service rate. |
| **Linear spring** | `x=F/k` within elastic range. | Stiffer spring compresses less for fixed load; extrapolate under new load without a new animation. |
| **Threshold** | TP/FP/TN/FN from explicit labels/scores and cutoff. | Raising a cutoff usually changes precision/recall tradeoffs, but don't claim universal monotonic precision on arbitrary data. |
| **Tokens** | Explicit tokenizer or simplified character segmentation; price from unit count. | Display exact segmentation of a chosen provided scheme; don't pretend toy segmentation predicts a production API's token IDs. |
| **Embedding** | Fixed vectors, similarity by specified distance/cosine. | Moving a vector updates true numeric similarity; geometric proximity means defined model similarity, not guaranteed semantic truth. |
| **RAG chunking** | Segment text with known chunk size/overlap; score/retrieve against query using an explicit metric. | Changing overlap affects boundaries and retrieval candidates; don't claim it necessarily improves answer quality. |
| **Agent** | Explicit state/action loop, stop conditions and tool feedback, with bounded steps. | Show why a loop stops/fails, not a decorative cycle. |
| **Economic flow** | Conservation accounting for funds moving among defined accounts. | Readouts balance at each step; distinguish estimates from real financial inputs. |

## 11. Existing host-app adapter: preserve the host

When the user asks for integration in an existing product, **inspect current code first**; do not interpret repository-specific examples as a universal API. Follow the host's lesson placement, existing figure IDs, supported `kind`, config types, feedback rules, localization and theme.

For the historically documented **AI English Shift** integration (only if the host is actually that app and its current code confirms the arrangement): its earlier convention used `src/explore/types.ts` (`FigureSpec`, `Text`, `pick`), `src/explore/Figure.tsx` (outer lesson frame), `src/explore/registry.ts` (kind→widget), `src/explore/specs` (lesson configs), `src/library/articles.json` (segments) and `src/explore/explore.css` (tokens). The spec anchor should reference an **actual sentence ID**. English/Chinese/Japanese/Korean learner-facing chrome was expected; validate actual current product requirements instead of copying an outdated interface blindly. Keep the widget restricted to the stage, with the outer frame owning prediction, explanation, writing frame and caveat. Lesson content belongs in config; the reusable widget owns mechanics. A newly named kind requires a real implementation and registration.

For a React host, use a pure semantic model, reusable geometry functions and a thin live component; avoid a browser `window` access on server render. Static HTML/paths can be server generated, but client movement uses refs/effects. If source mesh data is large, coordinate with the host's assets and bundle rules rather than forcing megabytes through its render props. All timers/listeners/observers need unmount cleanup. A port to a host may reasonably have multiple code files **inside that host**; that does not introduce another skill dependency.

Don't modify unrelated homepages, navigation, marketing galleries, learning maps, handoff flows, or publication branches merely to add one figure. Do not auto-publish without the user's request.

## 12. Verification: two independent acceptance gates

A visually polished but wrong model fails. A numerically correct model with a requested object that looks nothing like the subject also fails.

### A. Teaching / truth gate

- Anchor references the right sentence or stated claim; no duplicate IDs, invalid config, unsupported kind or missing required locales.
- Wrong prediction can be **actually challenged by an observable trial** without the answer already being disclosed. State the try condition and make controls cause a measurable output.
- The pure truth model is tested at reset, bounds, midpoints, discontinuities and meaningful transitions; calculation matches displayed number, physical orientation and text summary.
- The selected model correctly distinguishes semantic state from camera/part-selection/cutaway view state; visual exploration cannot silently alter the causal answer.
- Stochastic comparisons normalize expected probabilities, are reproducible under a fixed seed, do not treat a single batch as proof and offer a counterexample when a new misconception might form.
- The explanation contains **two causal facts**, not just the displayed answer. A transfer prompt tests a new case. Assumptions and omitted physics/history/anatomy are visible.
- For existing apps, run the **actual host validators** (schema, anchor, i18n, registration, bundle, behavior), not tests imagined from an earlier repository version.

### B. Visual / interaction gate

- Compare the actual silhouette, landmarks, material, volume and assembly with the specified object/reference. For cloth examine fold surfaces and overlap direction; for anatomy examine source alignment; for mechanics examine mounts, joint axes, tube ports, contact and cut wall thickness.
- Inspect **desktop and 360px**, supported light/dark themes, rest, midpoint and endpoint poses; no clipping, horizontal overflow, invisible labels or misleading depth order. Inspect captured images rather than merely producing them.
- Complete the main trial by pointer/touch **and** native/keyboard input. Check focus isolation from writing areas and site navigation. Check reset and repeated/reversed interactions (no stuck controls).
- Reduced motion: no forced auto-tour, pulse or essential-only animation; stepping and controls still reveal the model. Test pause/step for time simulations.
- Background/offscreen: no idle animation, dangling RAF, sound or held state; clean up on unmount/blur and visibility changes. Check Chrome/Chromium console for errors, stale assets, bad ARIA or hydration warnings.
- For dense assembled/cutaway models, zoom to contacts and inspect all explosion stages. A full intersection/occlusion audit requires suitable implemented geometry tests; the SVG painter helper alone is **not** such an audit.
- For 3D, inspect multiple orbit angles, joint limits and interpolated clips. Measure real frame/input timing on target screens. A quick orbit sample cannot be presented as a full 3D geometry pass.
- WebGL: graceful fallback, clipping, zero-at-boundary where needed, meaningful light theme, no context assumption and no physics validity claim from shader appearance.

### Verification evidence report (always be accurate)

```text
Truth model: [checks actually run, expected vs actual]
Interaction: [browser/device, pointer/keyboard/reset, or untested]
Visual fidelity: [specific poses and reference comparisons inspected, or draft/unverified]
Accessibility: [native controls, labels, reduced motion, keyboard, untested items]
Geometry / 3D: [audit coverage, performance measured or unverified]
Limits: [what is illustrative, what requires source data, what still needs verification]
```

Do not claim browser screenshots, user testing, medical accuracy, smooth FPS or audited intersections unless actually checked. A working code sample does not establish learning efficacy. Model tests and browser checks answer different questions from visual fidelity reviews.

## 13. Delivery rules

For every completed figure, deliver the **working self-contained artifact**, then briefly state: the claim/misconception, controls and readout, what is modelled vs illustrative, any source/assets used, and checks really performed. If the user requested a spec rather than working code, provide only that spec. If they requested a *single-file* result, inspect the output for **all** imports, `src=`, `href=` asset links, CDN fonts, `fetch()`, worker URLs or non-inlined resources. Internal fragment navigation and deliberate citations in prose are not runtime dependencies.

Use the host's established filename conventions. Do not create a file tree just because this guide describes concepts such as model, view and geometry: for standalone output, they are functions/modules **within the same HTML**. If an organic realism request exceeds what can be generated without source geometry, state the fidelity gap instead of labeling an impostor “done.” If build utilities aren't available, report limitations instead of inventing a check result.

### Minimal preflight checklist

```text
[ ] Exactly one skill file: FIGURES.md; no required supplemental skill/kit directories.
[ ] Claim, cause, effect, misconception and observable trial are specified.
[ ] One pure truth model powers picture, output, aria summary and explanation.
[ ] Renderer chosen for actual subject fidelity and inspection needs.
[ ] Main controls work using pointer/touch and keyboard-native equivalents.
[ ] Figure has reset, boundaries, labels, reduced-motion alternative, and no pre-answer autoplay.
[ ] Standalone HTML, if generated, has all JS/CSS/geometry embedded; no package/CDN/file fetch.
[ ] Tested numerical endpoints, browser behavior (if available), responsive layout and visual landmarks.
[ ] Historical/scientific source facts, calculations and illustrative numbers are distinguished.
[ ] Licenses and data-provenance requirements are honored; no verification overclaims.
```

## 14. Provenance, license and consolidation boundaries

This file consolidates the **reusable skill behavior**, teaching spec, renderer selection, light isometric SVG, mechanical construction, 3D/field techniques, six lesson-model recipes, host-app adapter concepts, verification standards and attribution from [ywEdAi/teachable-figures](https://github.com/ywEdAi/teachable-figures) (inspected 2026-10-31). The upstream repository was a **13.5 MB, 156-file working collection**, with offline demos and specialized toolkit/test/mesh assets. This is a **self-contained operational rewrite**, **not** a byte-for-byte archival copy or a reproduction of its large photographic previews, test harness, original builder sources, original extensive multi-body rendering engine, or ~4.5 MB atlas geometry. It replaces file dependencies with native APIs and inline recipes. Where those original bytes are essential to perfect fidelity, a generic renderer cannot replace them. Do not claim exact original visual parity.

Relevant original contributions:

- Teachable-figures: predict → try → explain; claim/anchor, misconception, model/spec, source distinction, teacher check and transfer.
- [Anatomy by Ryan](https://github.com/wheresryan22/anatomy), pinned in original source manifest at `454a0ef801b6d9b905f08309d094491c7085b3dd`: mechanical craft, solid geometry, attached parts, motion, true 3D and verification concepts. **The original toolkit code is not required or shipped here.**
- [iso-figure by Tolga Cohce](https://github.com/MrBongoC/ai-iso-skill), pinned in original source manifest at `44b4da7714148eef547cc1b102e433472dfe3531`: face-local projected functional objects. The short projection kernel in §6 is adapted from this MIT-licensed contribution.
- Human Reference Atlas / HuBMAP: the source repository's derived anatomy meshes are based on the HRA 3D Reference Library under [Creative Commons Attribution 4.0](https://creativecommons.org/licenses/by/4.0/), with cropping/quantization/recoloring. **No HRA mesh data is distributed in this Markdown file.** If an output embeds those meshes from an authorized data source, cite HRA/HubMAP, name alterations, link CC BY 4.0 and preserve source-specific attribution/provenance. A separately authored schematic diaphragm is not HRA reference data.

### MIT notice: Anatomy contribution

```text
MIT License
Copyright (c) 2026 Ryan

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

### MIT notice: iso-figure contribution

```text
MIT License
Copyright (c) 2026 Tolga Cohce

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

**End of skill.** Everything needed to follow this method and build self-contained schematic/procedural teaching figures is contained above. No accompanying reference files, rendering-kit folders, dependency manifests or install steps are required.
