# Babylon Projects

A small landing page linking out to a collection of standalone Babylon.js scenes. No build step — Babylon.js is loaded from the CDN in each project, so everything is static HTML that GitHub Pages can serve as-is.

## Structure

```
index.html                 landing page with links to each project
projects/
  store/index.html          "Cove Event Floor Plan" — camera/lights in code, model from cove-floorplan.glb
  store/cove-floorplan.glb   the floor plan model (~35MB)
  orbits/index.html         mini solar system with orbiting planets
  playground/index.html     materials/primitives showcase
  lenovo-event/index.html   "Lenovo Event" — placeholder, duplicated from store/
  lenovo-event/lenovo-floorplan.glb   placeholder model, to be replaced
```

`projects/store/` is the floor plan setup for National Teacher's Day at Cove Garden (the folder path is still `store` to avoid breaking existing links — only the page's title/content changed).

`projects/lenovo-event/` was duplicated from `store/` as a starting point and hasn't been customized yet — swap its `lenovo-floorplan.glb` for the real Lenovo event model the same way described below, and update its title/overlay text in `index.html`.

Each project page has a "&larr; Home" button (top-right) that links back to the landing page.

### Replacing cove-floorplan.glb

`projects/store/index.html` sets up its own camera and lights in code, then imports meshes from `cove-floorplan.glb` with `BABYLON.SceneLoader.ImportMeshAsync("", "./", "cove-floorplan.glb", scene)` and auto-frames the camera to the model's bounding box. To swap in a different model:

1. Export a `.glb` from Blender (File → Export → glTF 2.0, format "glTF Binary (.glb)") or any other tool.
2. Replace `projects/store/cove-floorplan.glb` with it, keeping the filename — or change the filename in `index.html`'s `ImportMeshAsync` call if you'd rather rename it.
3. Camera framing is automatic; no other code changes needed.

## Adding a new project

1. Create a new folder under `projects/`, e.g. `projects/my-scene/`.
2. Add an `index.html` inside it (copy an existing project as a starting point for the home button + loading screen boilerplate).
3. Add a card for it in the root `index.html` linking to `projects/my-scene/`.

## Run locally

Serve the repo root with any static server, e.g.:

```bash
npx serve .
```

Then open the printed local URL — the landing page is at `/`, and each project is at `/projects/<name>/`.

## Deploy on GitHub Pages

1. Push this repo to GitHub (already the case if you're reading this on GitHub).
2. In the repo, go to **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to `Deploy from a branch`, branch `main`, folder `/ (root)`.
4. Save. GitHub will publish the site at `https://<username>.github.io/<repo>/` within a minute or two.
