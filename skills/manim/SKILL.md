---
name: manim
description: >
  Expert guide for writing, debugging, and modifying mathematical animations with Manim.
  Use this skill whenever the user mentions "manim", "manimgl", "manim community", 
  "3Blue1Brown", "3Brown1Blue", or "3b1b" in the context of creating, fixing, or 
  understanding animation code — including requests like "write a manim scene for X",
  "how do I animate X in manim", "my manim scene isn't working", "can you help me with
  this manim code", or "I want to make a 3Blue1Brown-style video". Also trigger for
  beginners who just say they want to make math animations and don't know where to start.
---

# Manim Animation Expert

You are an expert at creating mathematical animations with Manim. Users may be on either:

- **ManimGL** (3b1b's personal version): `pip install manimgl`, imports `from manimlib import *`, runs with `manimgl`
- **Manim Community**: `pip install manim`, imports `from manim import *`, runs with `manim`

## Step 1: Establish the version

If the user hasn't said which version, check their code for the import line — `manimlib` means ManimGL, `manim` means Community. If there's no code yet, ask. When helping a complete beginner with no preference, recommend Community (it's more stable and has better documentation at manim.community).

---

## Core scene structure (identical in both)

```python
from manimlib import *   # or: from manim import *

class MyScene(Scene):
    def construct(self):
        circle = Circle()
        self.play(ShowCreation(circle))   # ManimGL
        # self.play(Create(circle))       # Community
        self.wait(1)
```

**Key scene methods** (both versions):
- `self.play(*animations, run_time=1)` — run animations simultaneously
- `self.wait(duration=1)` — pause
- `self.add(*mobjects)` — add to scene without animating
- `self.remove(*mobjects)` — remove from scene

---

## Mobject types and hierarchy

All visual objects are **Mobjects**. The most important subclass is **VMobject** (vector graphics built from quadratic Bézier curves), which nearly all 2D shapes and text inherit from.

**Shapes** (both versions):
```python
Circle(), Ellipse(), Arc()
Square(), Rectangle(), RoundedRectangle()
Line(), Arrow(), Vector(), DashedLine()
Polygon(), Triangle(), RegularPolygon(n=6)
Dot(), SmallDot()
```

**Text and LaTeX**:

| | ManimGL | Community |
|---|---|---|
| Plain text | `Text("hello")` | `Text("hello")` |
| LaTeX formula | `Tex(r"x^2 + y^2")` | `MathTex(r"x^2 + y^2")` |
| Mixed text+LaTeX | `TexText(r"Use $x^2$")` | `Tex(r"Use $x^2$")` |

**Grouping**:
```python
VGroup(a, b, c)   # Group of VMobjects, can be animated as one
Group(a, b)       # Generic group (for mixing types)
```

**Coordinate systems**:
```python
Axes(x_range=(-3, 3), y_range=(-2, 2))
NumberPlane()     # infinite grid
NumberLine()
ComplexPlane()
```

---

## Positioning

The coordinate system is centered at the origin. The frame is 8 units tall and ~14 units wide by default. Constants: `UP`, `DOWN`, `LEFT`, `RIGHT`, `IN`, `OUT`, `ORIGIN`, plus diagonal shortcuts `UL`, `UR`, `DL`, `DR`.

```python
mob.move_to(np.array([1, 2, 0]))   # absolute position
mob.move_to(other_mob)             # center on another mob
mob.shift(2 * RIGHT + UP)          # relative shift
mob.to_edge(UP)                    # push to frame edge
mob.to_corner(UL)                  # push to corner
mob.next_to(other, RIGHT, buff=0.3) # place beside, with gap
mob.align_to(other, LEFT)          # align edges
VGroup(a, b).arrange(DOWN, buff=0.5)  # stack with spacing
VGroup(a, b).arrange_in_grid(2, 3)    # grid layout (Community)
```

---

## Style

```python
mob.set_color(BLUE)
mob.set_fill(RED, opacity=0.5)
mob.set_stroke(WHITE, width=2)
mob.set_opacity(0.7)
mob.scale(2.0)
mob.rotate(PI / 4)           # radians; use DEG constant: 45 * DEG
mob.flip()                   # horizontal flip
```

Named colors (both versions): `RED`, `BLUE`, `GREEN`, `YELLOW`, `WHITE`, `BLACK`, `ORANGE`, `PINK`, `PURPLE`, `TEAL`, `GOLD`, `MAROON`. Each comes in shades A–E (lightest to darkest): `BLUE_A`, `BLUE_B`, … `BLUE_E`.

---

## Animations

```python
# Creation
ShowCreation(mob)       # ManimGL — draw stroke then fill
Create(mob)            # Community equivalent
Write(mob)             # stroke-by-stroke (good for text/equations)
DrawBorderThenFill(mob)
FadeIn(mob, shift=UP)  # fade in, optionally drift in from direction
FadeOut(mob)
GrowFromCenter(mob)    # Community
ShowIncreasingSubsets(mob)  # submobjects appear one by one

# Transform
Transform(mob1, mob2)              # mob1 morphs into mob2; mob1 remains
ReplacementTransform(mob1, mob2)   # mob1 is replaced by mob2; mob1removed
FadeTransform(mob1, mob2)          # cross-fade instead of morph
MoveToTarget(mob)                  # use after mob.generate_target(); mob.target.shift(...)
mob.animate.shift(RIGHT)           # .animate builder: any method call becomes animation

# Indication
Indicate(mob)                      # flash + scale briefly
Flash(mob)                         # burst of lines from point
ShowPassingFlash(mob)              # traveling highlight
Circumscribe(mob)                  # draw rectangle or circle around
FlashAround(mob)                   # ManimGL: flash that circles the mob
Wiggle(mob)                        # Community: brief wiggle

# Composition
AnimationGroup(anim1, anim2)                  # simultaneous
Succession(anim1, anim2)                      # sequential
LaggedStart(anim1, anim2, lag_ratio=0.2)      # staggered start
LaggedStartMap(FadeIn, vgroup, lag_ratio=0.1) # apply animation to each submobject
```

The `.animate` builder chains method calls into a smooth interpolation:
```python
self.play(circle.animate.scale(2).shift(RIGHT).set_color(RED))
```

**Common `play()` kwargs**: `run_time=2`, `rate_func=smooth` (or `linear`, `there_and_back`, `rush_into`, etc.), `lag_ratio=0.1`.

---

## Updaters (per-frame live updates)

Updaters let objects react continuously to the scene's state — great for dynamic labels, trackers, and dependent motion.

```python
# Basic updater — called every frame
mob.add_updater(lambda m, dt: m.rotate(dt))  # dt = elapsed seconds

# Attach a label that follows a dot
label.add_updater(lambda m: m.next_to(dot, UP))

mob.remove_updater(func)
mob.clear_updaters()
mob.suspend_updating()   # pause updaters temporarily
```

**ManimGL helpers** (in `manimlib/mobject/mobject_update_utils.py`):
```python
always(mob.move_to, target)         # mob always moves to target's center
f_always(mob.set_color, get_color)  # call method with return value of function
always_redraw(lambda: Circle())     # recreate the object each frame
always_shift(mob, direction=RIGHT, rate=0.5)
always_rotate(mob, rate=20 * DEG)
turn_animation_into_updater(anim)   # run an animation as a continuous updater
```

---

## ManimGL-specific features

### Interactive development shell
```python
class MyScene(Scene):
    def construct(self):
        circle = Circle()
        self.add(circle)
        self.embed()   # drops into IPython; scene stays live in window
        # In the shell, type: play, add, remove, clear, wait (no "self." needed)
        # Type touch() to interact with the window via mouse
        # Press r to reset camera, q to exit window interaction
```
This is the fastest way to develop and experiment — make a change in the shell and see it instantly.

### Camera frame (ManimGL)
In ManimGL, `self.frame` is an animatable Mobject representing the camera. You can animate it like anything else:
```python
self.play(self.frame.animate.shift(UP * 2))
self.play(self.frame.animate.scale(0.5))   # zoom in
self.frame.reorient(theta_degrees=-30, phi_degrees=70)  # for 3D
self.frame.add_updater(lambda m, dt: m.increment_theta(-0.05 * dt))  # slow orbit
```

### 3D scenes (ManimGL)
```python
class My3DScene(ThreeDScene):
    # default_frame_orientation = (-30, 70)  # theta, phi in degrees
    def construct(self):
        sphere = Sphere(radius=2)
        self.add(sphere)
        self.play(self.frame.animate.increment_phi(-20 * DEG))
        
        # Objects fixed to the screen (HUD/overlay), not in 3D space:
        label = Text("Label").fix_in_frame()
        label.to_corner(UL)
        self.add(label)
        
        # Textured surface
        TexturedSurface(sphere, day_texture_path, night_texture_path)
        SurfaceMesh(sphere).set_stroke(BLUE, 1, opacity=0.5)
```

### CLI (ManimGL)
```
manimgl file.py SceneName           # preview in window
manimgl file.py SceneName -w        # write to video
manimgl file.py SceneName -o        # write and open
manimgl file.py SceneName -s        # save last frame (no video)
manimgl file.py SceneName -n 4      # skip to 4th animation
manimgl file.py SceneName -l/-m/--hd/--uhd   # 480p/720p/1080p/4K
manimgl file.py SceneName -p        # presenter mode (space/→ to advance)
```

---

## Manim Community-specific features

### Camera control (Community)
```python
class MyScene(MovingCameraScene):
    def construct(self):
        self.play(self.camera.frame.animate.shift(UP))
        self.play(self.camera.frame.animate.scale(2))
```

### 3D scenes (Community)
```python
class My3DScene(ThreeDScene):
    def construct(self):
        self.set_camera_orientation(phi=75*DEGREES, theta=-45*DEGREES)
        self.begin_ambient_camera_rotation(rate=0.1)
        sphere = Sphere()
        self.add(sphere)
```

### CLI (Community)
```
manim file.py SceneName             # render (default quality)
manim -pql file.py SceneName        # preview, low quality (480p)
manim -pqm file.py SceneName        # preview, medium (720p)
manim -pqh file.py SceneName        # preview, high (1080p)
manim -pqk file.py SceneName        # preview, 4K
manim --save_last_frame file.py SceneName
```

---

## ValueTracker pattern (both versions)

A clean way to animate a value and have other objects react to it:
```python
t = ValueTracker(0)
dot = Dot()
dot.add_updater(lambda m: m.move_to(
    axes.c2p(t.get_value(), np.sin(t.get_value()))
))
self.play(t.animate.set_value(TAU), run_time=4)
```

---

## Troubleshooting

**LaTeX won't render**: LaTeX must be installed separately. Linux: `apt install texlive-full`. Mac: `brew install mactex`. The error will mention a `.tex` file failing to compile — check that `pdflatex` is on your PATH.

**"No module named manimlib"**: You installed the wrong package. Run `pip install manimgl` for ManimGL, `pip install manim` for Community.

**Transform morphs badly**: Make sure the two objects have the same number of submobjects/paths where possible. Use `ReplacementTransform` not `Transform` if you want the source removed from the scene.

**Updater not running**: Updaters fire during `play()` and `wait()` calls. They do not fire during `add()`. If you need an object to update before any animation, call `mob.update()` explicitly, or set `always_update_mobjects=True` on the scene.

**ManimGL window doesn't appear**: You may be missing OpenGL support (headless server). Use `-s` to save last frame instead of previewing, or `-w` to write directly to a video file.

**`self.embed()` drops you out immediately**: This usually means the scene finished `construct()` before the shell could launch. Make sure `self.embed()` is called before the end of `construct()`, not after a return or at the end of the method when there's nothing left.
