# Babylon Projects

A small landing page linking out to a collection of standalone Babylon.js scenes. No build step — Babylon.js is loaded from the CDN in each project, so everything is static HTML that GitHub Pages can serve as-is.

## Structure

```
index.html                 landing page with links to each project
projects/
  store/index.html          walkable store interior with shelving and products
  orbits/index.html         mini solar system with orbiting planets
  playground/index.html     materials/primitives showcase
```

Each project page has a "&larr; Home" button (top-right) that links back to the landing page.

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
