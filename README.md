# Babylon Projects

A single-page Babylon.js viewer with a project sidebar. No build step — Babylon.js is loaded from the CDN, so everything is static HTML that GitHub Pages can serve as-is.

## Structure

```
index.html                          the entire app: sidebar, viewer, all camera/light/UI code
projects/
  store/cove-floorplan.glb           "Cove Event Floor Plan" model (~35MB)
  lenovo-event/lenovo-floorplan.glb  "Lenovo Event" model (placeholder, to be replaced)
```

There's only one HTML page. The `PROJECTS` array near the top of `index.html`'s `<script>` lists each project (id, title, context line, and where its `.glb` lives); the sidebar is rendered from that array, and picking one loads its model into the same Babylon scene. The `projects/<name>/` folders now hold nothing but the model file — the per-project `index.html` pages that used to live there are gone.

Opening the site with no `#hash` loads `PROJECTS[0]` (currently Cove). Each project also gets its own URL — `/#cove`, `/#lenovo` — so links to a specific project are shareable and survive a refresh; the back/forward buttons work too.

## Adding a new project

1. Put its `.glb` somewhere under `projects/`, e.g. `projects/my-scene/my-scene.glb`.
2. Add an entry to the `PROJECTS` array in `index.html`:
   ```js
   { id: "my-scene", tag: "Scene 03", title: "My Scene", context: "Short description",
     rootUrl: "projects/my-scene/", fileName: "my-scene.glb" }
   ```
3. That's it — the sidebar entry, routing, camera framing, shadows, and lighting are all generic and driven by this array.

If the model has its own light node named `"Light"` (e.g. exported from Blender with a Sun), the viewer uses it for shadows automatically (including compensating for a case where the light's rotation lives on a parent node rather than the light itself). Otherwise it falls back to a generic directional light positioned above the model.

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
