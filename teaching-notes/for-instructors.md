# Notes for instructors

How this course was designed and run, for anyone teaching with it.

## Format

Four hands-on workshops of about an hour each, plus a pre-flight setup students complete on their own. In its first run (Summer 2026) each workshop was paired with a lecture on installation art in the same weekly session, and a fifth session was given to final project reviews. The workshops also stand on their own.

Students work in **one file that grows all course long.** Each session adds to the last, which makes the payoff of good habits (named Nulls, clean sections) visible by Session 4.

## Why the topics come in this order

1. **Images first (TOPs).** Instant visual feedback, so the very first hour ends with something working.
2. **Numbers next (CHOPs, MediaPipe).** The moment the software "sees" the student. Kept separate from 3D so that only one new kind of data arrives at a time.
3. **3D as a deliberate detour (SOPs, rendering), driven by the mouse.** This is the session with the most silent failures, so the input is kept as simple as possible.
4. **Assembly and craft.** Because the particles read a named Null, replacing the mouse with a hand is a single change. The rest of the hour is look and feel.

## The pre-flight routine

The [setup deck](../00-setup/) goes out **72 hours** before the first session. Students post two **proof-of-life screenshots** (live webcam, live hand tracking) with their OS and build number **48 hours** before. This tells you who is ready, and surfaces broken installs while there is still time to fix them.

Things to say twice:

- **Everyone on the same TouchDesigner build** (2025.33070). A mismatched build can refuse to open the course files.
- **`release.zip`, not "Source code (zip)",** from the MediaPipe releases page. This is the most common support problem.
- **The first MediaPipe launch is slow.** Thirty seconds of loading is normal; students tend to force-quit at twenty.
- **The class call and TouchDesigner cannot share one camera.** Students joining online should join the call from a second device.

Flag integrated-graphics machines early and prepare a lighter Session 3 file (fewer grid rows and columns), so that everyone still runs the same patch.

## Recurring teaching points

- **"Address, not a wire."** Say it every time a parameter-field connection appears. See [address-not-a-wire.md](address-not-a-wire.md).
- **Silent failures.** Stage them on purpose. In Session 3, build the mouse chain first and let nothing happen on screen; then type the expressions and let the mouse take hold. Show a render with the Render flag missing at one level. See [silent-failures.md](silent-failures.md).
- **The debugging method.** Click through the chain and read each node's output. The same method solves the Session 1 resolution collapse, the Session 2 range problems and the Session 3 black renders.
- **Ugly and working beats pretty and theoretical.** The Session 1 homework explicitly asks for the ugliest result.

## Homework and feedback by screen recording

From Session 2 on, homework is submitted as a **short screen recording** (about ten seconds) of the patch running, plus a written question or a line on what was changed. A still image cannot show interaction.

In the first run, recordings were posted to a shared Padlet board and answered in the post comments. The aim is a specific, technical answer diagnosed from what is visible in the recording: which node, which parameter, which flag. Generic advice does not help a student who is stuck.

## Final deliverable

A file that runs in Perform Mode and responds to **at least two things the student's hands do**, plus a **thirty-second recording** of them using it.

## How the patches were developed

The course patches were built and checked against a live TouchDesigner session through [touchdesigner-mcp](https://github.com/8beeeaaat/touchdesigner-mcp), a third-party open-source server that lets an AI agent read and edit a running TouchDesigner network. It made it practical to check the chains shown in the slides (operator names, parameter values, channel names) on the same build students would use. Neither the server nor any AI tool is needed to take or teach the course.
