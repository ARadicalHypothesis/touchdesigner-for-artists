# Session 2 · Motion becomes number

**The day the software starts seeing you.** By the end of the hour your fingertip is three live numbers. Everything after this session is deciding what those numbers should control.

Slides: [slides-02-motion-becomes-number.pdf](slides-02-motion-becomes-number.pdf) · Checkpoint: [session2_hand_numbers.tox](session2_hand_numbers.tox) · Homework reference: [homework2_pinch_circle.tox](homework2_pinch_circle.tox)

Open your Session 1 file before starting. We add to it; we do not start again. This session needs the MediaPipe plugin from [Session 0](../00-setup/) running in the same project.

## CHOPs: numbers instead of pictures

A **channel** is one number that keeps changing. A CHOP carries a bundle of them.

1. **It updates constantly,** sixty times a second, like everything else. A CHOP is a live reading, not a stored list.
2. **Channels have names.** Your hand arrives as dozens of named numbers; most of the work is picking the ones you want.
3. **Its viewer is a graph.** A flat line means nothing is coming through. That is your first debugging check.

## What MediaPipe actually gives you

MediaPipe watches the webcam and reports **21 points per hand**: wrist, knuckles, and the joints and tip of every finger, for up to two hands at once. (Joint names: MCP is the big knuckle at the palm, PIP the middle joint, DIP the one nearest the tip, TIP the end. We want TIP.)

The plugin hands you these landmarks already named, as ordinary CHOP channels, in the `hand_tracking` component's normalized data output. There is no magic in the tracking: your hand becomes coordinates, and coordinates are things you can add, scale, smooth and compare. Once you accept that, gesture control becomes arithmetic.

### Reading the channel names

```
h1:index_finger_tip:x
│  │                └─ axis: x, y or z
│  └─ landmark, in plain words
└─ which hand: the first one detected
```

Take **z** as well as x and y. x and y place a point on a flat screen; z is depth, how near your hand is to the camera. Sessions 3 and 4 are in 3D, so grab it now. In a Select CHOP, put the full names in **Channel Names**, separated by spaces.

Values arrive **normalized from 0 to 1, with the origin at the top-left and y counting downward**, so they need remapping before they drive anything in 3D.

## Two things that will confuse you

Both are real behaviours, not bugs. Details in [mediapipe-hand-data.md](../teaching-notes/mediapipe-hand-data.md).

- **`h1` is not "left" and `h2` is not "right".** `h1` is simply the first hand the camera noticed. Drop it and raise the other, and `h1` now means a different physical hand.
- **The image is mirrored, so handedness reads backwards.** The feed is flipped before MediaPipe sees it, so raising your right hand reads as "Left". It describes the mirrored picture, which matches what you see on screen.

For this course, treat them as "first hand" and "second hand".

## Build it · A line between thumb and fingertip

From two sets of numbers to a line on screen.

| Node | Type | Setting |
|---|---|---|
| `select_thumb` | Select CHOP | Thumb tip channels; **Rename To** `t*` |
| `select_index` | Select CHOP | Index fingertip channels; **Rename To** `t*` |
| `join1` | Join CHOP | Stacks them into 2 samples: sample 0 is the thumb, sample 1 the fingertip |
| `line1` | Line SOP | **Points** = 2: a blank two-point line |
| `chopto1` | CHOP to SOP | **CHOP** field `../join1`; `line1` wired in. Drops the points onto the samples. |
| `transform1` | Transform SOP | Remaps 0–1 into SOP space: **Scale** 2, −2, 1 · **Translate** −1, 1, 0 |
| `geo1` | Geometry COMP | Holds the chain above; **Material** ← `constant1` |
| `constant1` | Constant MAT | Flat colour; needs no light |
| `cam1` | Camera COMP | Default |
| `render1` | Render TOP | **Camera** ← `cam1`, **Geometry** ← `geo1` (drag them in) |

Why Rename To `t*`: the Join CHOP only merges channels with matching names, so both selects must read `tx ty tz`.

The Material, Camera and Geometry fields are **addresses, not wires**. Drop the node onto the field.

> **Two flags, two levels.** The Display flag feeds the viewers; the Render flag feeds the Render TOP. Both `transform1` (inside) and `geo1` (outside) need their **Render** flag on. One without the other gives an empty frame and no warning. If you see the line in a viewer but not in `render1`, it is always this.

## The Math CHOP · getting numbers into the right range

The tracking counts in one set of units; whatever you drive expects another, and nobody translates for you. This is the most common reason a connection "does nothing" or sends something flying off screen.

1. Watch the real numbers in the viewer as you move your hand. Note the lowest and highest values you actually reach.
2. Put those in the Math CHOP's **From Range**, and what the target needs in the **To Range**.

Two fixes that look like bugs: moving right sends it left (flip the sign: the mirrored image again), and moving up sends it down (screen coordinates count downward; same fix).

## If the tracking will not work

| Symptom | Likely cause |
|---|---|
| No numbers at all | MediaPipe has not finished starting, or another app has the camera. Quit everything else, restart, give it a full minute. |
| Was working, now frozen | **Cooking** got switched off. Right-click the component and check; a non-cooking component freezes on its last frame and looks like loading. |
| Numbers freeze halfway | Tracking lost your hand. Better light, plain background, hand fully in frame, palm toward the camera. |
| Channel names look different | Plugin versions vary. Use the names in *your* channel list. |

Everything downstream of your named Null neither knows nor cares where the numbers come from: live tracking, a recording, or a slider dragged by hand. That is the Null habit paying off.

## Homework · Pinch to prove it

Twenty minutes. Make **a number that gets smaller when you pinch thumb and finger together.**

1. **Add the thumb.** Add the thumb tip's channels alongside the index finger's.
2. **Measure the gap.** The distance between two points: close together, small number.
3. **Show it working.** Drive anything with it: a circle's size, a blur amount, the brightness of last session's effect.

Post a **ten-second screen recording**, not a screenshot. Movement is the point.

## Before Session 3

Save your file, post your recording, and close anything using your camera. Nothing new to install.
