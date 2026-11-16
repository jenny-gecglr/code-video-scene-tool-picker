---
name: code-video-scene-tool-picker
description: Picks the right code-based animation tool for a video scene. Numbers, charts, text and transitions go to HyperFrames; formulas to Manim; explosions and particles to PixiJS; falling or colliding objects to Rapier physics; orbiting 3D models to Three.js. Use when someone asks "how should we build this scene", or mentions formula, particle, physics, 3D or chart animation.
---

# Code Video Scene Tool Picker

Choose one animation tool per scene. Default to the simplest tool that works.

## Decision table

| What is in the scene | Tool | How |
|---|---|---|
| Numbers, counters, charts, text, transitions, overlays, captions | **HyperFrames** | Main editing tool. Root composition, timeline, GSAP (the animation library it uses). Browse the catalog with `npx hyperframes catalog --tag=transition`, `--tag=overlay` or `--tag=text`. |
| Math formulas, equations, diagram drawing | **Manim** | Python. Render a clip (`manim render -qh scene.py Scene`) and place it in the HyperFrames composition as a muted `<video>`. Formula typesetting (`MathTex`) needs a LaTeX install, so ask the user before using it if LaTeX is not installed; text and shapes work without it. |
| Explosions, particles, sparks, smoke, confetti | **PixiJS** (v8) | 2D WebGL canvas. Draw from the `hf-seek` event's `e.detail.time` (see Setup notes); use seeded randomness. |
| Heavy objects falling, collisions, piles | **Rapier** (2D/3D physics) | Pre-compute the simulation with a fixed step (for example 1/30 s), store positions per frame, then read them by time in `hf-seek`. This keeps it deterministic. |
| Camera orbiting a machine or model (3D) | **Three.js** | 3D canvas. Drive it from `hf-seek` and `e.detail.time` (see Setup notes); set the root composition's `data-duration` attribute (its total length). |

## Setup check (first run)

Before picking a tool, check what is installed. For anything missing that the chosen tool needs, say what it is and what it is for, then **ask before installing**.

| Tool | Check | Install |
|---|---|---|
| Node.js and HyperFrames | `node --version`, `npx hyperframes --version` | Install Node.js from nodejs.org; HyperFrames runs through `npx hyperframes` |
| Manim (only for formula scenes) | `manim --version` | `pip install manim` |
| LaTeX (only for `MathTex` formulas) | `latex --version` | Ask first; text and shapes work without it |
| PixiJS, Rapier, Three.js (only when a scene needs them) | the built file exists in the project's `lib/` folder | Install once in a shared tools folder with npm, then copy the built JavaScript file into `lib/` |

Install a tool only when a scene actually needs it. A project that only uses HyperFrames needs nothing else.

## Rules

1. **Start with HyperFrames.** Most scenes (illustrations with narration, transitions and captions) need nothing else. The other tools are for graphics-heavy scenes only.
2. **Formula on screen?** Use Manim.
3. **Physics not needed?** Skip Rapier; simple motion is easier with GSAP.
4. **Simple 2D effect?** Check the HyperFrames overlay catalog first (film grain, light leak, glitch). Use PixiJS only for custom particles.
5. **No 3D needed?** Skip Three.js; it is heavy. Keep previews light on low-memory machines.
6. **Stay deterministic.** No `Date.now()`, no unseeded randomness, no network requests. Every frame is a pure function of time.
7. **Preview before rendering.** Rendering is expensive, so stay in preview and let the user decide when to render.

## Setup notes

- Install the libraries once in a shared tools folder and copy the library's built JavaScript file into the project's `lib/` folder, so projects load nothing from the network.
- Versions this skill was tested with (check the current releases before relying on them): Three.js 0.187, PixiJS 8.23, Rapier 0.21 (the `-compat` npm build), HyperFrames CLI 0.9, Manim 0.22.
- `hf-seek` is the HyperFrames time-seek event: it fires with the current time (`e.detail.time`) so each frame can be drawn as a pure function of time.
