# Session 3 · Particles

**Ten thousand moving points, and how to get them on screen.** No hand tracking today, deliberately. The ideas are enough on their own; Session 4 connects them to your hands.

Slides: [slides-03-particles.pdf](slides-03-particles.pdf) · Checkpoint: [session3_particles.tox](session3_particles.tox)

This is the hardest session of the course. Not because the clicking is difficult, but because more things can fail **silently** today than on any other day. Keep the [silent failures list](../teaching-notes/silent-failures.md) open.

## How this particle system works

Start with a grid of points, push them around with a force, and draw what results.

1. **A grid gives you the points.** Ten thousand of them in a flat sheet. Not particles yet, just positions.
2. **The Particle SOP makes them move.** It takes those points as a starting arrangement and gives them physics: velocity, life, the ability to be pushed.
3. **A metaball is the thing that pushes.** An invisible ball of influence. Move it through the points and they scatter. This is what your hand drives in Session 4.

A metaball normally makes blobby 3D shapes. Here it is never drawn; only its field of influence is used, as a force.

## The network

```
Points   grid1 ─► transform1 ─► particle1 ─► geo2 (material constant1)
                                   ▲                ╎
Force    metaball1 ─► force1 ──────┘ inputs 1 and 2 ╎
            ╎ Center X / Z expressions              ╎
Mouse    mousein1 ─► math_mouse ─► null_mouse       ╎
                                                    ╎
Render   cam1 ╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌► render1p ─► out_render
```

Solid arrows are wires; `╌` marks an address typed or dragged into a parameter field.

| Node | Type | Settings |
|---|---|---|
| `grid1` | Grid SOP | Orientation ZX (flat) · Size 8 × 8 · Rows/Columns 100 |
| `transform1` | Transform SOP | Identity for now; a placeholder for moving the emitter |
| `particle1` | Particle SOP | Birth 1000 · Life 10 · Particle Type: lines · input 0 = source, inputs 1 and 2 = force |
| `metaball1` | Metaball SOP | Radius 1, 1, 1 · Weight 2 · Center Y 0 · Center X and Z ← expressions |
| `force1` | Force SOP | Radial 1.0 · Vortex 0.228 · Spiral 0.272 · input ← `metaball1` |
| `geo2` | Geometry COMP | Contains only an In SOP · Render flag on inside · Material ← `constant1` |
| `constant1` | Constant MAT | Flat colour for the particles |
| `cam1` | Camera COMP | Pull Translate Z back to frame the grid |
| `render1p` | Render TOP | 1280 × 720 · Geometry ← `geo2 geo_field` · Camera ← `cam1` · no lights |
| `mousein1` | Mouse In CHOP | Defaults; outputs `tx`, `ty` from −1 to 1 |
| `math_mouse` | Math CHOP | From −1…1 → To −4…4 (screen units to world units, so the cursor reaches the whole 8-unit grid) |
| `null_mouse` | Null CHOP | A stable name to address |
| `geo_field` + `constant_field` | Geometry COMP + Constant MAT | Optional: red, alpha 0.2, blending on, to make the force field visible |

## Build order

Each step has a visible result, or a deliberate lack of one.

1. `grid1` → `transform1` → `particle1`. Turn on the Particle SOP's viewer; the points drift. Nothing is pushing yet.
2. `metaball1` → `force1`, then `force1` into `particle1` inputs 1 and 2. The particles collapse to the origin.
3. **Make it visible.** Create `geo2`, go inside and **delete its default contents**, add an In SOP and turn its Render flag on. Back outside, wire `particle1` into `geo2`. Drag `constant1` onto its Material field. Create `cam1` and `render1p`, and **drag** `geo2` and `cam1` onto the Render TOP's Geometry and Camera fields.
4. `mousein1` → `math_mouse` → `null_mouse`. Watch the numbers; nothing on screen changes yet.
5. Type the expressions into `metaball1`. **This is the moment the mouse takes hold.**
6. Optional: add `geo_field` with `constant_field` to the Render TOP's Geometry field, to see the force field.

### The links with no wires

| Where | Parameter | What goes in |
|---|---|---|
| `metaball1` | Center X | `op('null_mouse')['tx']` |
| `metaball1` | Center Z | `op('null_mouse')['ty']` |
| `metaball1` | Center Y | `0` |
| `geo2` | Material | `constant1` |
| `render1p` | Geometry | `geo2 geo_field` |
| `render1p` | Camera | `cam1` |

Three mechanisms look alike. An **expression** turns the field blue and evaluates live. A **CHOP export** turns it green and needs the source CHOP's export flag on (an unfinished export stays green and reads zero). An **export table** is a Table DAT listing channel, target node and parameter.

## Nothing is appearing

All of these produce an identical black screen with **no error message**. Work down the list.

- **The render flag is off.** The small square at the *bottom-right* of the SOP inside your geo. New nodes do not inherit it. Bottom-left means "let me see it"; bottom-right means "include it when rendering". It must be on inside **and** on the Geometry COMP.
- **The geo still has its default contents.** A Geometry COMP arrives with a shape inside that renders in front of your work.
- **The Render TOP's fields are empty.** Drag the nodes in; a typed relative path can look accepted and silently stay empty.
- **The camera is pointing at nothing.** Your particles may be behind it. Pull Translate Z back.
- **Your points are microscopic.** Temporarily scale everything up to check they exist.
- **You used a lit material.** Phong and PBR need a Light COMP or they render black. Constant MAT does not, which is why we use it.

A black render is not an error. An empty or unlit scene is a legitimate thing to want, so TouchDesigner stays quiet. Silence means it did exactly what you asked.

**Radius is reach.** A metaball's field is exactly zero outside its radius. If the metaball moves but the particles do not react, check that the Force SOP goes into the Particle SOP's second input and that the radius is big enough to overlap the points.

## Why the mouse first

It is a rehearsal for Session 4 using something simpler than a hand. Next session you swap `null_mouse` for your hand's Null and the whole thing becomes hand-controlled. **One connection changes; nothing else in the patch has to know.** That is what the named Nulls were for.

## If your machine is struggling

Lower the grid's **Rows and Columns**; that is your particle count. Signs you need to: the frame rate at the bottom of the window drops well below 60, the fan gets loud, or dragging a parameter lags. Halve it, and halve again if needed. Fewer particles looks less dense and behaves identically, and your file stays compatible with everyone else's. Working out what your machine can handle is a normal part of this craft.

## Homework · Find one you like

Twenty minutes of play, one recording.

1. **Push it too far first.** Make the metaball radius absurdly large, then tiny. Change the grid density. Find the edges before you look for the middle.
2. **Then settle on one** setting you actually find beautiful.
3. **Write down what you changed,** one or two lines with your ten-second recording. Naming what you did turns an accident into a technique.

Whatever you land on is what your hands control in Session 4.
