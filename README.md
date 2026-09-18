# Babylon Projects

A single-page Babylon.js viewer with a project sidebar. No build step — Babylon.js is loaded from the CDN, so everything is static HTML that GitHub Pages can serve as-is.

## Structure

```
index.html                          the entire app: sidebar, viewer, all camera/light/UI code
projects/
  lenovo-event/lenovo-floorplan.glb  "Lenovo Event" model (placeholder, to be replaced) — humanoid walk animation
  server-cabinet/server-cabinet.glb  "Server Cabinet" model, with an openable door
```

There's only one HTML page. The `PROJECTS` array near the top of `index.html`'s `<script>` lists each project (id, title, context line, and where its `.glb` lives); the sidebar is rendered from that array, and picking one loads its model into the same Babylon scene. The `projects/<name>/` folders hold nothing but the model file — there are no per-project `index.html` pages.

Opening the site with no `#hash` loads `PROJECTS[0]` (currently Lenovo). Each project also gets its own URL — `/#lenovo`, `/#server-cabinet` — so links to a specific project are shareable and survive a refresh; the back/forward buttons work too.

## Adding a new project

1. Put its `.glb` somewhere under `projects/`, e.g. `projects/my-scene/my-scene.glb`.
2. Add an entry to the `PROJECTS` array in `index.html`:
   ```js
   { id: "my-scene", tag: "Scene 03", title: "My Scene", context: "Short description",
     rootUrl: "projects/my-scene/", fileName: "my-scene.glb" }
   ```
3. That's it — the sidebar entry, routing, camera framing, shadows, and lighting are all generic and driven by this array.

If the model has its own light node named `"Light"` (e.g. exported from Blender with a Sun), the viewer uses it for shadows automatically (including compensating for a case where the light's rotation lives on a parent node rather than the light itself). Otherwise it falls back to a generic directional light positioned above the model.

### Footer buttons

The bottom bar is the same fixed set of buttons for every project — Save, Portrait, Landscape, 30°, 60°, Shadows, Orbit — each icon paired with a short label. Nothing in this bar is model-specific; anything that only applies to some models (the collections panel, animation/state toggles) lives in the right-side panels instead, described below. Portrait/Landscape/30°/60° only change the camera's angle (`alpha`/`beta`) — they leave `camera.radius` (zoom/distance) exactly as it was, so switching views never yanks the camera closer or farther from the model. Shadows calls `shadowGenerator.removeShadowCaster()`/`.addShadowCaster()` on every caster and toggles `receiveShadows`. Orbit plays/pauses the idle auto-rotate (see below) independently of whatever caused it to stop before — pressing it always resumes the spin, even after clicking a view-preset button.

### Right-side panels: Collections and Animations

Two panels can appear top-right, each independent and only shown when the loaded model actually has something for it:

- **Collections** — one checkbox per top-level Blender Collection (each with its objects parented under an Empty named after the collection, then exported to glTF). Detection is automatic and generic: any node parented directly at the scene root, with no geometry of its own and at least one child, counts as a group — except a multi-material mesh's `"<name>_primitiveN"` wrapper node and an Armature object (Babylon's glTF loader treats the armature itself as the skeleton's first bone, not a collection). Toggling calls `node.setEnabled(...)`, which cascades to every descendant.
- **Animations** — one checkbox per animation-like "state" the model offers. Every embedded glTF `AnimationGroup` gets a row (named after the group, e.g. Lenovo's `"X.SHOPPING"` walk cycle) — checked plays it looped, unchecked stops it. A project can set `stopEmbeddedAnimation: true` (see server-cabinet) to default that row to off instead of on — used when the embedded animation would otherwise fight a custom scripted toggle over the same bone every frame. Server Cabinet also gets a **Cabinet Door** row: if the model has a skeleton bone named `"bone1"` (the door hinge), checking it animates that bone's linked transform node's `rotationQuaternion` to a 128° rotation around the Y axis; unchecking returns it to identity. `AnimationGroup.stop()` alone doesn't reliably stop its underlying per-node Animatables in the Babylon version this project pins — `stopAnimationGroupFully()` also stops every `Animatable` returned by `scene.getAllAnimatablesByTarget()` for that group's targets, which is what actually works.

### Per-model zoom speed

Babylon's default `wheelPrecision` (3) is an absolute step size, not scaled to the model — it felt right on the ~28-unit Cove floor plan but much too fast on the ~8-unit server cabinet. After framing the camera, the viewer sets `camera.wheelPrecision = Math.max(3, 84 / modelSize)`, so smaller models automatically get a slower, finer scroll-zoom without needing a per-project setting.

## Camera controls

Plain Babylon.js defaults (same as the [Babylon.js Sandbox](https://sandbox.babylonjs.com/)) — no custom overrides:

- Left-drag: orbit around the model
- Scroll: zoom
- Right-drag: pan

The camera idles by slowly auto-rotating until the user interacts (drags, or clicks a view-preset button) or presses the Orbit footer button to pause it explicitly.

## Responsive layout

The sidebar is a slide-in drawer (hamburger button, top-left) on phones and tablets, and a persistent panel that pushes the viewer over on wider screens (≥900px) — toggle it either way with the same button. Selecting a project on a narrow screen closes the drawer automatically.

## Run locally

Serve the repo root with any static server, e.g.:

```bash
npx serve .
```

Then open the printed local URL.

## Deploy on GitHub Pages

1. Push this repo to GitHub (already the case if you're reading this on GitHub).
2. In the repo, go to **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to `Deploy from a branch`, branch `main`, folder `/ (root)`.
4. Save. GitHub will publish the site at `https://<username>.github.io/<repo>/` within a minute or two.
