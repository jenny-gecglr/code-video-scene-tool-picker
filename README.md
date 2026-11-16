# Video Scene Tool Picker

A Claude skill that picks the right code-based animation tool for each scene of a video, so you do not reach for a heavy tool when a simple one works.

## What problem it solves

Code-driven video offers many tools, and choosing wrongly wastes hours: a physics engine for a simple fall, a 3D engine for a flat chart, a particle system where an overlay would do. This skill gives Claude one decision table and a few firm rules.

## The decision table

| What is in the scene | Tool |
|---|---|
| Numbers, counters, charts, text, transitions, overlays, captions | **HyperFrames** (the default) |
| Math formulas, equations, diagram drawing | **Manim** |
| Explosions, particles, sparks, smoke, confetti | **PixiJS** |
| Heavy objects falling or colliding | **Rapier** (physics) |
| Camera orbiting a machine or model | **Three.js** |

## Rules it enforces

- Start with HyperFrames; the others are for graphics-heavy scenes only.
- Skip Rapier when no physics is needed, and Three.js when no 3D is needed.
- Check the HyperFrames overlay catalog before building a custom 2D effect.
- Stay deterministic: no `Date.now()`, no unseeded randomness, no network requests; every frame is a pure function of time.
- Preview before rendering.

## Requirements

HyperFrames and, depending on the scene, Manim (Python), PixiJS, Rapier and Three.js. Tested versions: Three.js 0.187, PixiJS 8.23, Rapier 0.21, HyperFrames CLI 0.9, Manim 0.22. Formula typesetting in Manim needs a LaTeX install.

## Install

Copy the `video-scene-tool-picker` folder into `~/.claude/skills/`.

## Example prompts

- "How should we build this scene with an equation on screen?"
- "I want an explosion of confetti at the end. Which tool?"
- "This scene shows boxes falling onto a table. What do we use?"

## Files

- `SKILL.md`: the decision table and rules Claude follows.

## License

MIT. See [LICENSE](LICENSE).
