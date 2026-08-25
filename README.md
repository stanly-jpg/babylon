# Babylon Store

A small Babylon.js portal: a walkable/orbitable 3D store with shelving units, products, a checkout counter, and a rotating showcase item. Pure static HTML — no build step, Babylon.js is loaded from the CDN.

## Run locally

Just open [index.html](index.html) in a browser, or serve the folder with any static server, e.g.:

```bash
npx serve .
```

## Controls

- Left-drag: orbit the camera
- Scroll: zoom
- Right-drag: pan

## Deploy on GitHub Pages

1. Push this repo to GitHub (already the case if you're reading this on GitHub).
2. In the repo, go to **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to `Deploy from a branch`, branch `main`, folder `/ (root)`.
4. Save. GitHub will publish the site at `https://<username>.github.io/<repo>/` within a minute or two.
