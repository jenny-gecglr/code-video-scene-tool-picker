---
name: video-scene-tool-picker
description: Picks the right code-based animation tool for a video scene. Numbers, charts, text and transitions go to HyperFrames; formulas to Manim; explosions and particles to PixiJS; falling or colliding objects to Rapier physics; rotating 3D models to Three.js. Use when someone asks "how should we build this scene", or mentions formula, particle, physics, 3D or chart animation.
---

# Video Scene Tool Picker

Choose one animation tool per scene. Default to the simplest tool that works.

## Decision table

| What is in the scene | Tool | How |
|---|---|---|
| Numbers, counters, charts, text, transitions, overlays, captions | **HyperFrames** | Main editing tool. Root composition, timeline, GSAP animation. Browse the catalog with `npx hyperframes catalog --tag=transition\|overlay\|text`. |
| Math formulas, equations, diagram drawing | **Manim** | Python. Render a clip (`manim render -qh scene.py Scene`) and place it in the HyperFrames composition as a muted `<video>`. Formula typesetting (`MathTex`) needs a LaTeX install; text and shapes work without it. |
| Explosions, particles, sparks, smoke, confetti | **PixiJS** (v8) | 2D WebGL canvas. Draw from the `hf-seek` event's `e.detail.time`; use seeded randomness. |
| Heavy objects falling, collisions, piles | **Rapier** (2D/3D physics) | Pre-compute the simulation with a fixed step (for example 1/30 s), store positions per frame, then read them by time in `hf-seek`. Deterministic. |
| Camera orbiting a machine or model (3D) | **Three.js** | 3D canvas. Drive it from `hf-seek` and `e.detail.time`; set the root `data-duration`. |

## Rules

1. **Start with HyperFrames.** Most scenes (illustration + narration + transition + captions) need nothing else. The other tools are for graphics-heavy scenes only.
2. **Formula on screen?** Use Manim. **Physics not needed?** Skip Rapier; simple motion is easier with GSAP.
3. **Simple 2D effect?** Check the HyperFrames overlay catalog first (film grain, light leak, glitch). Use PixiJS only for custom particles.
4. **No 3D needed?** Skip Three.js; it is heavy. Keep previews light on low-memory machines.
5. **Stay deterministic.** No `Date.now()`, no unseeded randomness, no network requests. Every frame is a pure function of time.
6. **Preview before rendering.** Rendering is expensive; confirm the preview first.

## Setup notes

- Install the libraries once in a shared tools folder and copy the needed file into the project's `lib/` folder, so projects load nothing from the network.
- Tested versions: Three.js 0.187, PixiJS 8.23, Rapier 0.21 (`-compat`), HyperFrames CLI 0.9, Manim 0.22.
