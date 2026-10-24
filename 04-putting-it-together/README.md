# Session 4 · Putting it together

**Your hands, your particles, and making it look like something.** Both halves already exist. Today is one connection, then an hour of craft.

Slides: [slides-04-putting-it-together.pdf](slides-04-putting-it-together.pdf) · Checkpoint: [session4_hand_particles.tox](session4_hand_particles.tox) · Instance shapes: [instance-shapes/](instance-shapes/)

## The finished network

A hand-tracked, instanced particle field with trails, composited over the camera image.

It has four parts. Solid arrows are wires; `╌` marks an address (an expression or a node dragged into a parameter field).

```
1 Hand      hand_tracking ─► select_index ─► math_hand ─► null
                                                           ╎ Center X / Z expressions
2 Particles grid1 ─► transform2 ─► particle1 ◄─ force1 ◄─ metaball1
                                       │
3 Shapes                         inst_positions ╌► INST_sphere (material pbr1)
                                                        ╎
            cam1, light1 ╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌► render1p
                                                        │
4 Look      trail_feedback ─► trail_fade ─► trail_comp ◄┘   (feedback target: trail_comp)
            MediaPipe/video ─► video_levels ─► comp_feed ◄─ trail_comp
                                                   │
                                               out_render
```

| Node | Type | What it does | Key settings |
|---|---|---|---|
| `hand_tracking` | MediaPipe COMP | Hand detection on the webcam; 21 landmarks per hand as normalized 0–1 channels | `h1:*:x/y/z` |
| `select_index` | Select CHOP | Picks the index-fingertip channels and renames them `x`, `y` | |
| `math_hand` | Math CHOP | Remaps 0–1 screen space to −4…4 world units, so the finger reaches the whole grid | From 0→1, To −4→4 |
| `null` | Null CHOP | The stable handle the metaball reads | channels `x`, `y` |
| `grid1` | Grid SOP | The point field, upright | Orientation XY · 8 × 8 · 100 × 100 = 10,000 points |
| `transform2` | Transform SOP | Identity placeholder for moving the emitter later | |
| `metaball1` | Metaball SOP | Invisible force-field shape; its center follows the fingertip; its radius is the reach of the force | Center X ← `op('null')['x']` · Center Z ← `op('null')['y']` · Center Y 0 · Radius 1.5 · Weight 4 |
| `force1` | Force SOP | Turns the metaball field into forces | Radial 2.5 · Vortex 0.15 · Spiral 0.361 · Axial 0.222 |
| `particle1` | Particle SOP | Born on the grid, pushed by the force, expiring after their life | Point sprites · Birth 3000 · Life 3 · Life variance 0.5 · Drag 1 |
| `inst_positions` | SOP to CHOP | Converts particle positions into `tx ty tz` channels the instancer can read | SOP ← `particle1` |
| `INST_sphere` | Geometry COMP | Draws a small sphere at every particle | Instance source ← `inst_positions` · Number of instances ← expression `op('inst_positions').numSamples` · Material ← `pbr1` |
| `pbr1` | PBR MAT | Light-reactive material, so each sphere shades differently | Base colour 0.9 / 0.85 / 0.24 · Roughness 0.22 · Metallic 0.19 |
| `light1` | Light COMP | The light PBR needs, off to one side | Position 5, 4, 6 |
| `cam1` | Camera COMP | Orthographic, facing the grid: maps the field 1:1 to the screen | Ortho width 8 · Translate Z 10 |
| `render1p` | Render TOP | Draws the instanced spheres on a transparent background | Geometry ← `INST_sphere` · Camera ← `cam1` · Lights ← `light1` · background alpha 0 |
| `MediaPipe/video` | Out TOP | The annotated camera image | |
| `video_levels` | Level TOP | Brightness and contrast on the video | Brightness 1.04 |
| `trail_feedback` | Feedback TOP | Hands back the previous frame so trails accumulate | Target ← `trail_comp` |
| `trail_fade` | Level TOP | Dims the fed-back history each pass; this decay is what makes trails fade | Opacity 0.88 |
| `trail_comp` | Composite TOP | New particles over the faded history; its output becomes the next frame's history | Over |
| `comp_feed` | Composite TOP | Lays the trailed particle layer over the video | Over |
| `out_render` | Out TOP | Final output | |

## Build it

1. **Swap the mouse for your hand.** Point the metaball's expressions at the hand's Null instead of `null_mouse`. Wave. Something should move.
2. **Fix the range.** Adjust the Math CHOP until your hand's full reach maps to the full width of the scene. Watch the real numbers as you move.
3. **Add depth (optional).** Drive a third axis with the hand's `z` channel, so pushing your hand toward the camera moves the force through the scene.
4. **Smooth it.** Put a **Lag CHOP** before the Null and raise it until the movement feels good rather than twitchy. This is taste, not a correct answer.
5. **Add the second hand.** A second metaball driven by the second hand, feeding the same force chain. Two hands, two pushers.
6. **Save** before you start experimenting. You will want to come back to this exact state.

## Giving the particles a shape · instancing

Instancing draws one small shape thousands of times, once per particle.

1. Put a small shape inside your Geometry COMP: a Sphere, Box or Circle SOP. Ready-made versions are in [instance-shapes/](instance-shapes/) (`INST_sphere.tox`, `INST_circle.tox`).
2. On the geo's **Instance** page, turn instancing on and point it at the particle positions.
3. **Keep the shape small.** A sphere of radius 1 drawn ten thousand times is one solid blob.

A circle or flat card is cheapest and looks almost identical at particle scale. A box gives a hard, digital feel; a sphere is softer but costs more. Try all three.

Two warnings from Session 3 still apply: instancing makes everything heavier (lower the grid density first if the frame rate drops), and a lit material like PBR needs a Light COMP or the whole thing goes black.

## More forces

Layering forces is where it gets interesting. **Add one at a time:** add, look, keep or remove, then add the next. Stack three at once and you will not know which one went wrong.

- **A second metaball,** driven by your other hand. Two pushers produce far more complex motion than one.
- **Wind:** a constant push in one direction. Gives the scene a sense of weather.
- **Turbulence:** small random movement that stops the motion looking mechanical. A little goes a long way.
- **Drag,** on the Particle SOP itself. High drag is heavy and syrupy; low drag makes them fly.

## Making it look good

Chain these after the Render TOP and end in a named Null, the same discipline as Session 1:

```
render1p ─► blur ─► add ─► level ─► null_final
```

- **Bloom:** a Blur, then an Add composite back over the original. Bright areas glow. This single move does more for the look than anything else.
- **Colour:** a Ramp TOP and a Lookup, or a Level's tint controls. Particles do not have to be white.
- **Contrast:** a Level TOP. Crush the blacks and the image reads as deliberate rather than a default render.
- **Trails:** the feedback loop from Session 1, applied to the render.

## Showing it to someone

- **Perform Mode.** Add a Window COMP pointed at your final Null. Press **F1** (or the Perform button) to go fullscreen with no interface; **Esc** brings you back. This is what you would run at an exhibition.
- **Recording.** A Movie File Out TOP after the final Null writes video to disk. For documentation, a plain screen recording is usually easier, and it shows you moving, which is the point.

## Final deliverable

A file that runs in Perform Mode and responds to **at least two things your hands do**, plus a **thirty-second recording** of you using it.

## Where to go from here

What the course deliberately skipped, and what each unlocks:

- **Python:** automate repetitive work, build custom interfaces, respond to events.
- **GLSL shaders:** write your own effects instead of combining existing ones. Steep, but it is where distinctive looks come from.
- **Audio reactivity:** an Audio Device In CHOP is not much harder than the Mouse In you used.
- **POPs:** the newer, GPU-native particle family. Worth exploring once these concepts are solid.
- **Building components:** package your own work as a `.tox`, the way MediaPipe was handed to you.
- **Sending signals out:** OSC, DMX and NDI, to drive lights, other software or a second machine. This is how installations get built.

Every item is a variation on what you have already done: nodes, wires, addresses, and reading what each step actually outputs.
