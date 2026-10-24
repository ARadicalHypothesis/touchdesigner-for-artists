# Session 1 · Streams and conversion

**Your first working patch, in one hour.** By the end you have a live webcam effect running. It will be ugly. That is the point: ugly and working beats pretty and theoretical.

Slides: [slides-01-streams-and-conversion.pdf](slides-01-streams-and-conversion.pdf) · Checkpoint: [session1_streams.tox](session1_streams.tox) · Homework reference: [homework1_break_it.tox](homework1_break_it.tox)

## One idea before we touch anything

TouchDesigner is **streams of data, and boxes that convert between them.** The rest is learning which boxes exist and how to plumb them together.

- **Nothing runs top to bottom.** There is no script executing line by line. Data flows left to right through whatever you have wired, sixty times a second.
- **A box is a verb.** Every node does one thing to what comes in and passes the result on: blur, add, select, smooth.
- **The craft is conversion.** A picture into numbers, numbers into movement, movement into a picture again.

## Four families you will actually use

| Family | Stands for | Carries | Its viewer shows |
|---|---|---|---|
| TOP | Texture Operator | Images: webcam, video, blur, colour | The image itself |
| CHOP | Channel Operator | Numbers over time: a slider, a fingertip, audio | A moving line graph. This is where interaction lives. |
| SOP | Surface Operator | 3D geometry: points, lines, shapes | A wireframe. Session 3 is built on these. |
| DAT | Data Operator | Text and tables, notes, Python | Rows of text |

Two more arrive later: **COMP** (Component, a box holding a whole network) and **MAT** (Material, how a 3D surface looks).

## Three moves and you can build anything

1. **Press Tab to make a node.** Click empty space, press Tab, type the node name, press Enter.
2. **Drag wire to wire to connect.** From the stub on a node's right edge to the stub on the next node's left edge. Output to input, always.
3. **Click the bottom-left corner square to see what a node does.** Without this you are working blind. (The bottom-right square is something else; it appears in Session 3.)

Scroll to zoom, middle-drag to pan, and press **H** to fit everything on screen when you get lost.

## What we build

Six nodes, each doing one thing to the picture:

```
cam ─► level1 ─► blur1 ─► composite1 ─► null_look ─► out1
                              ▲   │
                              └── feedback1 ◄┘
```

| Node | Type | Job |
|---|---|---|
| `cam` | Video Device In TOP | Your webcam, live |
| `level1` | Level TOP | Brightness and contrast |
| `blur1` | Blur TOP | Softens it |
| `composite1` | Composite TOP | Mixes this frame with the last one |
| `feedback1` | Feedback TOP | Holds the last frame and feeds it back in. This is what makes the smear. |
| `null_look` | Null TOP | The labelled outlet. Everything downstream reads this. |
| `out1` | Out TOP | Sends the result out of the container |

## Build it

Node names are what you type after pressing Tab.

1. **`videodevicein`** — your camera. Click its viewer square; you should see yourself. Black means another app has the camera.
2. **`level`** — wire the camera into it. Push Brightness and Contrast until it looks wrong, then back off.
3. **`blur`** — wire Level into it. Set Size to about 4.
4. **`composite`** — wire Blur into its *first* input. Nothing interesting yet.
5. **`feedback`** — wire Composite into Feedback, then Feedback back into Composite's *second* input. Yes, a loop. Watch what happens.
6. **`null`** — wire Composite into it and rename it `null_look`. It changes nothing; the next section explains why it matters.

## Why we added a node that does nothing

Think of a Null as **a labelled outlet on the wall.**

- *Without one,* ten things plug straight into your Blur. Add a node before it and you rewire ten connections, and any expression naming that Blur breaks.
- *With one,* ten things plug into `null_look`. Rebuild everything behind it however you like and nothing downstream notices.

Put one at the end of each section, where one part hands its result to the next, not after every node. Give it a real name: `null_look`, `null_hand`, `null_particles`.

## Address, not a wire

The second way things connect in TouchDesigner, and you will meet it every session. Some connections are not cables: they are **an address in a parameter field**, an instruction to "use *that* node for this setting". You drag a node onto a parameter field, or type its path, and no cable appears. Try it now: drag any node onto another node's parameter and watch the field fill in.

A wrong address fails **silently**, with no error at all, so it is always the first thing to check when nothing happens. More in [address-not-a-wire.md](../teaching-notes/address-not-a-wire.md).

## When everything suddenly looks like a postage stamp

Your picture collapses to a tiny blurry square and nothing you change fixes it. It will happen to you, and the symptom tells you nothing about the cause.

- **What happened:** a Feedback TOP starts life at 128×128 pixels. Because it loops back into the Composite, it drags the resolution of everything downstream down to match.
- **The fix:** on the Composite, set **Output Resolution** to Custom and point its width and height at the Blur before it. The whole chain snaps back to full size.

The habit to take away: when something looks wrong, click through the chain node by node and check the resolution at each step. The place where it changes is the place where the problem is.

## Homework · Break it three ways

Fifteen minutes. The goal is curiosity, not correctness.

1. **Blur → Size.** Push it to 100, then to 0. Notice how one number changes the whole feel.
2. **The feedback amount.** Take it up until the image never fades, then find where it just barely holds a trail.
3. **Composite → Operation.** Try difference, then multiply, then add: the same maths as photo-editing blend modes.

Post one screenshot of your **ugliest** result. Ugly means you pushed a parameter past where it was comfortable, and that is how you find out what it does.

## Before Session 2

- Save your file. You build on it every session.
- Make sure the MediaPipe example still opens and tracks your hand.
- Close Zoom and anything else using your camera before class.
