# Babylon Projects

A small landing page linking out to a collection of standalone Babylon.js scenes. No build step — Babylon.js is loaded from the CDN in each project, so everything is static HTML that GitHub Pages can serve as-is.

## Structure

```
index.html                 landing page with links to each project
projects/
  store/index.html          loads store.babylon via SceneLoader
  store/store.babylon        the store's scene data (placeholder — swap this file out)
  orbits/index.html         mini solar system with orbiting planets
  playground/index.html     materials/primitives showcase
```

Each project page has a "&larr; Home" button (top-right) that links back to the landing page.

### Replacing store.babylon

`projects/store/` loads its scene from `store.babylon` (Babylon's native JSON scene format) instead of building meshes in code — `index.html` there just does `BABYLON.SceneLoader.Load("./", "store.babylon", engine, ...)`. The current file is a placeholder (a ground plane + a box). To swap in a real scene:

1. Export/produce a `.babylon` file (e.g. from the Blender or Unity exporters, or via `BABYLON.SceneSerializer.Serialize(scene)` from a scene you've built in code).
2. Replace `projects/store/store.babylon` with it, keeping the filename.
3. `index.html` doesn't need any changes — it loads whatever scene the file defines, including its own camera and lights.

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
