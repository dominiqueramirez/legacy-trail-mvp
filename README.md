# Legacy Trail (MVP)

A quiet 3D walk of remembrance featuring ten Veterans whose stories appear in **VA News** and the **Veterans Legacy Memorial (VLM)**.

## Running Locally

From this folder, start a local HTTP server:

```bash
python3 -m http.server 8000
```

Then open [http://localhost:8000](http://localhost:8000) in your browser.

## Controls

| Action | Desktop | Mobile |
| --- | --- | --- |
| Move | `WASD` or Arrow keys | On-screen joystick |
| Look | Mouse (pointer lock or click-drag) | Drag on screen |
| Switch camera view | `V` | Camera button |
| Visit headstone | `E` or click | Tap |
| Salute / Hand over heart | `1` / `2` | Buttons on memorial card |
| Leave flag / flower / stone | `3` | Buttons on memorial card |
| Sound on or off | `M` | Sound button |

## Project Files

- `index.html` — Three.js 3D scene, avatar/gestures, audio controls, and memorial card UI (no build step).
- `veterans.json` — Hand-built dataset for the 10 featured Veterans.
- `photos/` — Local copies of VLM portraits and U.S. Air Force emblem fallback.
- `audio/` — Looping morning ambient soundscape (`ambient.wav`) and public-domain U.S. Army Band *Taps* (`taps.mp3`), muted by default.
- `vendor/` — Local copy of `three.module.min.js` for offline/zero-latency local demos.
