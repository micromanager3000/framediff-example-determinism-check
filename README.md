# FrameDiff example · determinism check

The M0 determinism proof, rebuilt on the [`framediff`](https://github.com/micromanager3000/framediff) library (it replaces
the old throwaway `spikes/m0` POC). A composition that is a **pure function of the frame** is
rendered **twice** for each spot-check frame via the library's
`checkCompositionDeterminism()`, and the
**pre-encode pixels are SHA-256'd** — equal hashes ⇒ deterministic on this machine.

```sh
git clone --recurse-submodules https://github.com/micromanager3000/framediff-example-determinism-check.git
cd framediff-example-determinism-check
npm install
npm run dev
```

Open in Chrome/Edge/Brave/Arc — it auto-runs on load and shows each frame's thumbnail, its hash,
and a ✅/❌, plus an overall verdict (`data-testid="verdict"`).

## What it exercises

- **The composition** ([`DetCheck.html`](src/compositions/DetCheck.html)) uses the plain `onFrame` lifecycle
  and `interpolate` helper and depends on **nothing but the frame** — no
  `Date.now`, `Math.random`, or `requestAnimationFrame`.
- **The render check** lives in the `framediff` package. The Svelte page calls
  `checkCompositionDeterminism(composition, { frames, width, thumbnails: true })`; the package uses
  the same DOM-rasterization path as export, so the example contains reporting UI rather than a
  second determinism utility.

Cross-*machine* pixel-identity isn't promised (fonts/GPU differ), but frame *selection* is exact
everywhere. See [`M0-FINDINGS.md`](https://github.com/micromanager3000/framediff/blob/main/docs/M0-FINDINGS.md) for the original spike's
measured results.

## FrameDiff dependency

Until the FrameDiff packages are published to npm, this repository pins the engine source in
`vendor/framediff`. Update it with `git submodule update --remote vendor/framediff`, then commit the
new gitlink after the example passes its checks.
