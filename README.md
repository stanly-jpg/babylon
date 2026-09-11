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

Babylon's `SceneLoader.ImportMeshAsync` doesn't auto-play a model's embedded animation — by default the viewer starts it looped (`animationGroup.play(true)`) once loaded, which is what gives Lenovo's humanoid its walk cycle. A project can instead set `stopEmbeddedAnimation: true` (see server-cabinet) to keep a model static — needed when something else needs full manual control of a bone the embedded animation would otherwise also drive. `AnimationGroup.stop()`/`.dispose()` and `scene.stopAllAnimations()` do *not* reliably stop its underlying per-node Animatables in the Babylon version this project pins — stopping each one directly (`scene.animatables.slice().forEach(a => a.stop())`) is what actually works.

### Server Cabinet's door button

`server-cabinet.glb` has a skeleton with a bone named `"bone1"` — the door hinge. On load, the viewer searches every loaded model's skeletons for that bone name; if found, it shows the door-toggle button (hidden otherwise, e.g. for Lenovo) and animates that bone's linked transform node's `rotationQuaternion` between identity and a 128° rotation around the Y axis. To give another project the same button, its skeleton just needs a bone named `"bone1"` — no other code changes needed.

### Per-model zoom speed

Babylon's default `wheelPrecision` (3) is an absolute step size, not scaled to the model — it felt right on the ~28-unit Cove floor plan but much too fast on the ~8-unit server cabinet. After framing the camera, the viewer sets `camera.wheelPrecision = Math.max(3, 84 / modelSize)`, so smaller models automatically get a slower, finer scroll-zoom without needing a per-project setting.

## Camera controls

Plain Babylon.js defaults (same as the [Babylon.js Sandbox](https://sandbox.babylonjs.com/)) — no custom overrides:

- Left-drag: orbit around the model
- Scroll: zoom
- Right-drag: pan

The camera buttons (bottom of the viewer) are unaffected by this and still work as before: Download PNG (transparent background), Top (Vertical), Top (Horizontal), Diorama 30°, Diorama 60°.

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
