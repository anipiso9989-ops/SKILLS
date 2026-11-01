---
name: standalone-3d-world
description: Create rich, interactive, educational 3D worlds, scientific models, visual effects, and physics simulations in a single self-contained standalone HTML file using Three.js. Combines standalone procedural synthesis with modern ES module import maps, PBR materials, GLSL shaders, post-processing bloom, CSS2D spatial labels, and interactive telemetry HUDs. Use whenever asked to generate a 3D scene, interactive 3D environment, educational model, product visualizer, or standalone 3D simulation in HTML.
---
# standalone-3d-world

Comprehensive super-skill for engineering scientifically accurate, visually stunning, interactive 3D worlds, simulations, and educational models in a single self-contained HTML file.

## When to Use

Use this skill whenever the user asks for:

- An interactive 3D simulation, scientific model, or virtual world (astronomy, biology, physics, mechanical engineering, architecture, chemistry, earth science).
- A standalone, single-file HTML 3D visualization or playable 3D experience.
- An educational 3D representation of an object, system, or process with spatial labels, cutaway views, shaders, and interactive controls.

## Core Architectural Principles

1. **100% Self-Contained Execution**: The HTML file must run directly in any browser or iframe with zero build steps, local servers, or external asset files.
2. **Modern ES Module Import Map**: Use a version-pinned ES module `<script type="importmap">` so you can import core `three` alongside official addons (`OrbitControls`, `EffectComposer`, `UnrealBloomPass`, `OutlinePass`, `CSS2DRenderer`, `RoomEnvironment`) cleanly.
3. **Zero External Asset Fragility**: Never load external `.gltf`, `.glb`, `.obj`, `.hdr`, or `.jpg`/`.png` URLs. Remote assets frequently break due to CORS or dead links. Synthesize all textures, bump maps, and environments procedurally in code.
4. **Photorealism via Procedural PMREM**: Metallic (`metalness > 0.5`) and transmissive (`transmission > 0`) PBR materials look black or flat without an environment map. Always generate a procedural environment map at startup using `THREE.PMREMGenerator` and `RoomEnvironment`.

## Master Standalone HTML Boilerplate

Use this modern ES Module structure as the foundation for every standalone 3D world:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Interactive 3D World</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body { overflow: hidden; background: #07090e; color: #f0f4f8; font-family: system-ui, -apple-system, sans-serif; user-select: none; }
    #canvas-container { position: fixed; inset: 0; z-index: 1; }
    #label-container { position: fixed; inset: 0; pointer-events: none; z-index: 2; }
    .spatial-label {
      padding: 4px 8px; border-radius: 6px; background: rgba(10, 14, 23, 0.82);
      border: 1px solid rgba(100, 200, 255, 0.35); color: #e2e8f0; font-size: 12px;
      pointer-events: auto; cursor: pointer; transition: transform 0.15s, border-color 0.15s;
      backdrop-filter: blur(6px); white-space: nowrap;
    }
    .spatial-label:hover { border-color: #38bdf8; transform: scale(1.06); }
  </style>
  <script type="importmap">
    {
      "imports": {
        "three": "https://cdn.jsdelivr.net/npm/three@0.170.0/build/three.module.js",
        "three/addons/": "https://cdn.jsdelivr.net/npm/three@0.170.0/examples/jsm/"
      }
    }
  </script>
</head>
<body>
  <div id="canvas-container"></div>
  <div id="label-container"></div>
  <!-- Educational HUD & Controls Overlay -->
  <script type="module">
    import * as THREE from 'three';
    import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
    import { RoomEnvironment } from 'three/addons/environments/RoomEnvironment.js';
    import { CSS2DRenderer, CSS2DObject } from 'three/addons/renderers/CSS2DRenderer.js';
    import { EffectComposer } from 'three/addons/postprocessing/EffectComposer.js';
    import { RenderPass } from 'three/addons/postprocessing/RenderPass.js';
    import { OutlinePass } from 'three/addons/postprocessing/OutlinePass.js';
    import { UnrealBloomPass } from 'three/addons/postprocessing/UnrealBloomPass.js';
    import { OutputPass } from 'three/addons/postprocessing/OutputPass.js';

    // Scene, Camera, Renderer, and Simulation setup here
  </script>
</body>
</html>
```

## Step-by-Step Implementation Workflow

### 1. Renderer, Color Space, and Procedural Environment

Configure the renderer for physically accurate output and initialize a procedural environment map so PBR materials reflect light naturally:

```javascript
const renderer = new THREE.WebGLRenderer({ antialias: true, powerPreference: 'high-performance' });
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.outputColorSpace = THREE.SRGBColorSpace;
renderer.toneMapping = THREE.ACESFilmicToneMapping;
renderer.toneMappingExposure = 1.1;
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;
renderer.localClippingEnabled = true; // Required for interactive cutaway views
document.getElementById('canvas-container').appendChild(renderer.domElement);

const scene = new THREE.Scene();
scene.background = new THREE.Color(0x07090e);
scene.fog = new THREE.FogExp2(0x07090e, 0.012);

// Procedural HDR environment for realistic metallic and glass reflections
const pmremGenerator = new THREE.PMREMGenerator(renderer);
scene.environment = pmremGenerator.fromScene(new RoomEnvironment(), 0.04).texture;
pmremGenerator.dispose();
```

### 2. Three-Point Lighting and Tightly Fitted Shadows

Supplement the environment map with structured lights:

- **Key Light**: `THREE.DirectionalLight` casting shadows. Fit `shadow.camera.left`, `right`, `top`, `bottom`, `near`, and `far` tightly around the scene bounding box so the `2048x2048` shadow map is crisp, not pixelated. Set `shadow.bias = -0.0005` to prevent shadow acne.
- **Fill Light**: Opposite-side `THREE.DirectionalLight` or `THREE.HemisphereLight` at 25% to 40% intensity to soften harsh shadows.
- **Rim / Accent Lights**: Colored `THREE.PointLight` or `THREE.SpotLight` behind or inside the subject to create edge separation and highlight internal cores or energy pathways.

### 3. Physically Plausible Geometry and Component Hierarchy

Never use a single bare sphere or box to represent a complex object. Compose hierarchical sub-assemblies using `THREE.Group`:

- **Constructive Hierarchy**: Parent sub-parts logically (e.g. `solarSystem -> planetGroup -> planetMesh + atmosphereMesh + moonPivot -> moonMesh`) so local rotations and orbital transforms compose naturally.
- **Advanced Geometries**:
  - `THREE.LatheGeometry` for axially symmetric vessels, nozzles, bells, craters, and biological chambers.
  - `THREE.ExtrudeGeometry` with `THREE.Shape` and `holes` for gears, structural ribs, cross-sectional organs, and architectural floorplans.
  - `THREE.TubeGeometry` along `THREE.CatmullRomCurve3` for arteries, nerves, pipes, wiring harnesses, and magnetic field lines.
  - `THREE.InstancedMesh` for rendering hundreds or thousands of repeated elements (stars, molecules, red blood cells, bolts, foliage) in a single GPU draw call.

### 4. Advanced PBR Materials (`MeshPhysicalMaterial`)

Pair these materials with `scene.environment` for photorealistic surface response:

```javascript
// Optical Glass / Lens / Laboratory Vessel
const glassMaterial = new THREE.MeshPhysicalMaterial({
  color: 0xffffff, metalness: 0.0, roughness: 0.05,
  transmission: 0.95, thickness: 0.6, ior: 1.52,
  envMapIntensity: 1.2, clearcoat: 1.0, clearcoatRoughness: 0.05,
  side: THREE.DoubleSide
});

// Biological Membrane / Translucent Organelle
const membraneMaterial = new THREE.MeshPhysicalMaterial({
  color: 0x38bdf8, emissive: 0x0284c7, emissiveIntensity: 0.15,
  metalness: 0.0, roughness: 0.25, transmission: 0.72, thickness: 1.2, ior: 1.38,
  attenuationColor: new THREE.Color(0x0369a1), attenuationDistance: 2.0,
  clearcoat: 0.8, clearcoatRoughness: 0.2
});

// Brushed Aerospace Titanium / Machined Metal
const machinedMetal = new THREE.MeshPhysicalMaterial({
  color: 0xcbd5e1, metalness: 0.92, roughness: 0.28,
  anisotropy: 0.75, anisotropyRotation: Math.PI / 4,
  clearcoat: 0.3, clearcoatRoughness: 0.2, envMapIntensity: 1.4
});
```

### 5. Procedural 2D Canvas Texture Synthesis

Generate rich diffuse (`map`), roughness (`roughnessMap`), and bump (`bumpMap`) textures on an offscreen `<canvas>` element. Set `texture.colorSpace = THREE.SRGBColorSpace` for color maps, and keep data maps (bump, roughness, normal) in linear space:

```javascript
function createNoiseStrataTexture({
  width = 512, height = 512,
  palette = ['#1e3a8a', '#1d4ed8', '#3b82f6', '#93c5fd'],
  bands = 12, turbulence = 28, isColor = true
} = {}) {
  const canvas = document.createElement('canvas');
  canvas.width = width;
  canvas.height = height;
  const ctx = canvas.getContext('2d');

  for (let y = 0; y < height; y++) {
    const v = y / height;
    const wave = Math.sin(v * Math.PI * bands) * 0.5 + 0.5;
    const idx = Math.min(palette.length - 1, Math.floor(wave * palette.length));
    ctx.fillStyle = palette[idx];
    ctx.fillRect(0, y, width, 1);
  }

  for (let i = 0; i < 180; i++) {
    const x = Math.random() * width;
    const y = Math.random() * height;
    const r = Math.random() * turbulence + 6;
    const grad = ctx.createRadialGradient(x, y, 1, x, y, r);
    grad.addColorStop(0, palette[i % palette.length]);
    grad.addColorStop(1, 'transparent');
    ctx.fillStyle = grad;
    ctx.beginPath();
    ctx.arc(x, y, r, 0, Math.PI * 2);
    ctx.fill();
  }

  const texture = new THREE.CanvasTexture(canvas);
  texture.wrapS = THREE.RepeatWrapping;
  texture.wrapT = THREE.RepeatWrapping;
  if (isColor) texture.colorSpace = THREE.SRGBColorSpace;
  return texture;
}
```

### 6. Custom GLSL `ShaderMaterial` Templates

Use custom shaders for effects that standard materials cannot express: atmospheric halos, energy flow along pathways, thermal heatmaps, and holographic scans.

#### Atmospheric Fresnel Rim Glow Shader
```javascript
function createFresnelGlowMaterial(glowColor = 0x38bdf8, power = 3.0, intensity = 1.4) {
  return new THREE.ShaderMaterial({
    uniforms: {
      uGlowColor: { value: new THREE.Color(glowColor) },
      uPower: { value: power },
      uIntensity: { value: intensity }
    },
    vertexShader: `
      varying vec3 vNormalWorld;
      varying vec3 vViewDir;
      void main() {
        vec4 worldPos = modelMatrix * vec4(position, 1.0);
        vNormalWorld = normalize(mat3(modelMatrix) * normal);
        vViewDir = normalize(cameraPosition - worldPos.xyz);
        gl_Position = projectionMatrix * viewMatrix * worldPos;
      }
    `,
    fragmentShader: `
      uniform vec3 uGlowColor;
      uniform float uPower;
      uniform float uIntensity;
      varying vec3 vNormalWorld;
      varying vec3 vViewDir;
      void main() {
        float fresnel = pow(1.0 - abs(dot(vNormalWorld, vViewDir)), uPower);
        gl_FragColor = vec4(uGlowColor * fresnel * uIntensity, fresnel);
      }
    `,
    transparent: true,
    blending: THREE.AdditiveBlending,
    depthWrite: false,
    side: THREE.BackSide
  });
}
```

#### Animated Directional Energy / Fluid Flow Shader
```javascript
function createFlowTubeMaterial(baseColor = 0x0ea5e9, pulseColor = 0x7dd3fc) {
  return new THREE.ShaderMaterial({
    uniforms: {
      uTime: { value: 0 },
      uSpeed: { value: 2.5 },
      uBaseColor: { value: new THREE.Color(baseColor) },
      uPulseColor: { value: new THREE.Color(pulseColor) }
    },
    vertexShader: `
      varying vec2 vUv;
      void main() {
        vUv = uv;
        gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
      }
    `,
    fragmentShader: `
      uniform float uTime;
      uniform float uSpeed;
      uniform vec3 uBaseColor;
      uniform vec3 uPulseColor;
      varying vec2 vUv;
      void main() {
        float wave = pow(0.5 + 0.5 * sin(vUv.x * 24.0 - uTime * uSpeed), 4.0);
        vec3 col = mix(uBaseColor, uPulseColor, wave);
        gl_FragColor = vec4(col, 0.85);
      }
    `,
    transparent: true
  });
}
```

### 7. Post-Processing Pipeline (`EffectComposer`)

Post-processing operates in linear color space before `OutputPass` applies tone mapping and converts to sRGB:

```javascript
const composer = new EffectComposer(renderer);
composer.addPass(new RenderPass(scene, camera));

const outlinePass = new OutlinePass(
  new THREE.Vector2(window.innerWidth, window.innerHeight),
  scene,
  camera
);
outlinePass.edgeStrength = 3.5;
outlinePass.edgeGlow = 0.6;
outlinePass.edgeThickness = 1.5;
outlinePass.visibleEdgeColor.set('#38bdf8');
outlinePass.hiddenEdgeColor.set('#0284c7');
composer.addPass(outlinePass);

const bloomPass = new UnrealBloomPass(
  new THREE.Vector2(window.innerWidth, window.innerHeight),
  0.45, // strength
  0.35, // radius
  0.82  // threshold: only meshes with emissiveIntensity > 0.85 bloom
);
composer.addPass(bloomPass);
composer.addPass(new OutputPass());
```

### 8. Floating 3D-to-2D Spatial Annotations (`CSS2DRenderer`) and Raycasting

Pin crisp, clickable HTML callout badges directly to 3D coordinates and support pointer raycasting:

```javascript
const labelRenderer = new CSS2DRenderer();
labelRenderer.setSize(window.innerWidth, window.innerHeight);
document.getElementById('label-container').appendChild(labelRenderer.domElement);

function attachSpatialLabel(targetMesh, title, onSelect) {
  const div = document.createElement('div');
  div.className = 'spatial-label';
  div.textContent = title;
  div.addEventListener('pointerdown', (e) => {
    e.stopPropagation();
    onSelect(targetMesh);
  });

  const labelObj = new CSS2DObject(div);
  targetMesh.geometry.computeBoundingBox();
  const topY = targetMesh.geometry.boundingBox.max.y;
  labelObj.position.set(0, topY + 0.35, 0);
  targetMesh.add(labelObj);
  return labelObj;
}
```

### 9. Frame-Rate-Independent Camera Fly-To & Damping

Use `THREE.MathUtils.damp(current, target, lambda, delta)` for smooth, frame-rate-independent transitions of camera positions and OrbitControls targets:

```javascript
const cameraState = {
  active: false,
  targetPos: new THREE.Vector3(),
  targetLookAt: new THREE.Vector3()
};

function flyToComponent(mesh) {
  const box = new THREE.Box3().setFromObject(mesh);
  const center = box.getCenter(new THREE.Vector3());
  const size = Math.max(box.getSize(new THREE.Vector3()).length(), 1.2);

  cameraState.targetLookAt.copy(center);
  const offset = mesh.userData.cameraOffset || new THREE.Vector3(size * 1.1, size * 0.7, size * 1.3);
  cameraState.targetPos.copy(center).add(offset);
  cameraState.active = true;
}

controls.addEventListener('start', () => { cameraState.active = false; });

function updateCameraDamping(delta) {
  if (!cameraState.active) return;
  const lambda = 6.0;
  camera.position.x = THREE.MathUtils.damp(camera.position.x, cameraState.targetPos.x, lambda, delta);
  camera.position.y = THREE.MathUtils.damp(camera.position.y, cameraState.targetPos.y, lambda, delta);
  camera.position.z = THREE.MathUtils.damp(camera.position.z, cameraState.targetPos.z, lambda, delta);

  controls.target.x = THREE.MathUtils.damp(controls.target.x, cameraState.targetLookAt.x, lambda, delta);
  controls.target.y = THREE.MathUtils.damp(controls.target.y, cameraState.targetLookAt.y, lambda, delta);
  controls.target.z = THREE.MathUtils.damp(controls.target.z, cameraState.targetLookAt.z, lambda, delta);

  if (camera.position.distanceTo(cameraState.targetPos) < 0.02) {
    cameraState.active = false;
  }
}
```

### 10. Interactive Cutaways, Exploded Views, Instancing, and Live Telemetry HUD

- **Interactive Cross-Section Clipping**: Enable `renderer.localClippingEnabled = true`, attach `[new THREE.Plane(new THREE.Vector3(0, 0, -1), constant)]` to outer shell materials (`side: THREE.DoubleSide`), and bind `plane.constant` to a HUD slider.
- **Exploded-View Assembly**: Store `restPosition` and `explodedPosition` in `mesh.userData` and interpolate with `mesh.position.lerpVectors(rest, exploded, factor)` via a HUD slider.
- **High-Count Particle Systems (`InstancedMesh`)**: Render thousands of corpuscles, molecules, stars, or debris in a single draw call by updating instance matrices with a shared `THREE.Object3D` dummy and setting `instancedMesh.instanceMatrix.needsUpdate = true`.
- **Live 2D Telemetry Sparklines**: Embed a compact `<canvas width="240" height="70">` inside the HUD inspector card to plot real-time simulation metrics (pressure, velocity, voltage, or orbital energy) alongside the 3D view.

## Common Mistakes and Modern Fixes

| Outdated / Broken Pattern | Modern Verified Pattern | Why It Matters |
| :--- | :--- | :--- |
| Loading external `.glb` or `.jpg` URLs | Procedural `BufferGeometry` + `THREE.CanvasTexture` | External URLs fail in sandboxes due to CORS, 404s, or offline viewing. |
| Calling `clock.getElapsedTime()` then `clock.getDelta()` | Call `clock.getDelta()` ONCE per frame and accumulate `elapsedTime += delta` | `getElapsedTime()` internally calls `getDelta()`, zeroing out a subsequent `getDelta()` call and freezing animations. |
| `renderer.outputEncoding = THREE.sRGBEncoding` | `renderer.outputColorSpace = THREE.SRGBColorSpace` | `outputEncoding` was removed in modern Three.js releases. |
| Metallic or glass materials rendering solid black | Generate `scene.environment` with `PMREMGenerator` + `RoomEnvironment` | PBR metals and transmissive glass require an environment map to compute reflections and refraction. |
| Snapping `camera.position.copy(...)` on click | Frame-rate-independent `THREE.MathUtils.damp()` on `camera.position` and `controls.target` | Instant camera jumps disorient the user; damped interpolation provides cinematic spatial continuity. |
| Using `window.innerWidth` for raycasting inside containers | Use `canvas.getBoundingClientRect()` for pointer normalization | Prevents pointer offset bugs when the canvas is resized or embedded. |
| Creating geometries or `Vector3` inside `animate()` | Pre-allocate scratch vectors (`const _v = new THREE.Vector3()`) outside the loop | Prevents garbage-collection frame stutter and WebGL memory leaks. |
| Coplanar overlapping meshes flickering | Set `material.polygonOffset = true; material.polygonOffsetFactor = -1;` or `logarithmicDepthBuffer: true` | Eliminates Z-fighting on layered surfaces, decals, and astronomical distance scales. |
