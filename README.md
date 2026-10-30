# Meta_splat
Simple Gaussian Splat viewer over html for Meta Quest headsets with the following readme

# SplatXR

A small, open-source Gaussian splat viewer that runs in the Meta Quest browser (Quest 2, 3, Pro) and on desktop.
One HTML file, no build step, no dependencies. MIT licensed.

## Features

- Loads `.splat` files and standard 3D Gaussian Splatting `.ply` files
- Six sample scenes, any cross-origin-friendly link, or a file from your device (also drag and drop)
- Stereo WebXR rendering with depth sorting in a background worker
- Splat budget setting that keeps the most visible splats, so big scenes stay smooth on Quest 2
- Shareable links: `index.html?url=https://example.com/scene.splat` (add `&flip=0` for Y-up scenes)

## Run it on your Quest

WebXR only works over `https://` (or `localhost`), so host the file somewhere first.

**GitHub Pages (free):** create a repo, add `index.html`, then Settings → Pages → deploy from the `main` branch.
Open `https://<you>.github.io/<repo>/` in the Quest Browser and press **Enter VR**.

**Local testing:** `npx serve .` on your computer, then use `adb reverse tcp:3000 tcp:3000` with the Quest plugged
in and open `http://localhost:3000` in the Quest Browser.

## Controls

| In the headset | |
|---|---|
| Grip or trigger | Grab the splat to move and turn it |
| Both grips | Pull apart to scale, twist to rotate |
| Right stick | Left/right spins, up/down resizes |
| Left stick | Walk through the scene |
| A / B | Next sample / reset placement |
| X / Y | Flip upside-down / toggle spin |

| On a screen | |
|---|---|
| Drag | Orbit |
| Right-drag or Shift-drag | Pan |
| Scroll or pinch | Zoom |
| R / F / Space | Reset / flip / spin |

## Notes

- Links must send CORS headers. Hugging Face (`/resolve/` links), GitHub raw and most CDNs do; Google Drive and
  Dropbox share pages don't.
- Compressed formats (`.ksplat`, `.spz`, `.sog`, SuperSplat compressed `.ply`) aren't supported yet. Convert them
  to `.splat` or a standard `.ply` with [SuperSplat](https://superspl.at/editor).
- Colours use the base (degree-0) spherical harmonics only, which keeps it fast on mobile GPUs.
- On Quest 2, 250k–500k splats is a comfortable range; go higher on Quest 3.
- Sample scenes are hosted at `huggingface.co/cakewalk/splat-data` and come from public 3DGS datasets.

## How it works

Each splat's position, colour and 3D covariance are packed into a 32-bit integer texture. A worker sorts splats
back to front with a 16-bit counting sort whenever the view changes, and the GPU draws one instanced quad per splat,
projecting its covariance into a screen-space ellipse (EWA splatting) for each eye.

