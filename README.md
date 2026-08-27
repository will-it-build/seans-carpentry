# Sean's Carpentry

A site for a bespoke joinery workshop, built live for **Will It Build** — build 003.

Two complete layouts ship side by side. A toggle in the footer of each switches between them.

| | Layout | Idea |
|---|---|---|
| **01** | `index.html` | Paper-and-pencil drawing-office feel. A sticky hero where scrolling scrubs a video of a floor being laid — scroll down and the boards assemble. Cut list, four-step process, timber swatches. |
| **02** | `alt/index.html` | Darker, editorial. A CSS-only 3D floor of ~40 generated planks flies apart as you scroll, with the real video fading in over the top once it has a frame. |

## How the scroll-scrub works

Neither version plays the video. Both seek it — scroll position maps to `video.currentTime`, so the footage becomes a timeline you drag with the wheel.

That is fiddly in practice, and both files guard against the same three failures:

- **Seek storms.** Setting `currentTime` before the last seek resolves stalls the decoder. A `seeking` flag plus a 450 ms timeout keeps one seek in flight and recovers if `seeked` never fires.
- **iOS won't decode until you play.** Safari ignores `preload="auto"` on cellular and paints nothing until `play()` has been called once. Both versions call `play()` then immediately `pause()` to prime it, and retry on first touch.
- **Seeking may not work at all.** Version 01 falls back to an ambient loop on `error`. Version 02 renders a CSS 3D floor underneath and only fades the video in once it has a frame, so there is always something on screen.

Motion is damped rather than snapped (`current += (target - current) * k`), and `prefers-reduced-motion` drops the scrub entirely and shows a finished floor.

## Running it

Static — no build step, no dependencies beyond Google Fonts.

```bash
python3 -m http.server 8000
```

Then open `http://localhost:8000`. Serve it rather than opening the file directly; `file://` blocks the video seeking.

## Contents

```
index.html          version 01
alt/index.html      version 02
floor.mp4           hero footage, ~2.4 MB
floor-poster.jpg    poster frame
```

---

Business details in the copy are placeholder. Part of [Will It Build](https://will-it-build.com) — apps and sites built live with AI, then open sourced.
