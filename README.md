# Manim Skill for Claude Code

A Claude Code skill that helps you create mathematical animations with [Manim](https://github.com/ManimCommunity/manim) — covering both **ManimGL** (3Blue1Brown's original library) and **Manim Community**.

## What it does

- **Write scenes from scratch** — describe the animation you want and get working Python code
- **Debug broken scenes** — paste your code and get a diagnosis with a fix
- **Navigate the API** — know which classes, methods, and patterns to reach for without switching to docs
- **Handle both versions** — ManimGL and Community share a name but differ in imports, CLI, camera control, and key class names; the skill identifies which you're using and adapts

## Coverage

| Topic | Covered |
|---|---|
| ManimGL (`manimgl` / `from manimlib import *`) | ✓ |
| Manim Community (`manim` / `from manim import *`) | ✓ |
| 2D scenes, shapes, text, LaTeX | ✓ |
| Animations, transforms, transitions | ✓ |
| Updaters and `ValueTracker` | ✓ |
| 3D scenes and camera control | ✓ |
| ManimGL interactive shell (`self.embed()`) | ✓ |
| CLI flags and render quality options | ✓ |
| LaTeX setup and troubleshooting | ✓ |

## Installation

First, add the marketplace (one-time):

```
/plugin marketplace add hartnady/manim-skill
```

Then install the plugin:

```
/plugin install manim@hartnady-plugins
```

## Trigger phrases

The skill activates when you mention **manim**, **manimgl**, **3Blue1Brown**, or **3b1b** in the context of making or fixing animations — for example:

- *"Write a manim scene that shows the Pythagorean theorem"*
- *"My manimgl camera zoom isn't working"*
- *"How do I animate a matrix transformation in manim community?"*
- *"I want to make a 3Blue1Brown-style explainer video"*

## About the two versions

[ManimGL](https://github.com/3b1b/manim) is Grant Sanderson's personal fork, used to produce 3Blue1Brown videos. It uses OpenGL for rendering, has an interactive IPython embed mode, and is installed as `manimgl`. [Manim Community](https://github.com/ManimCommunity/manim) is a stable, well-documented fork maintained by the open-source community, installed as `manim`. They are not interchangeable — the skill knows the differences.
