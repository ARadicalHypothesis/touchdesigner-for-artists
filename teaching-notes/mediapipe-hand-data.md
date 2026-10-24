# MediaPipe hand data in TouchDesigner

Notes on the hand tracking component of the [MediaPipe TouchDesigner plugin](https://github.com/torinmb/mediapipe-touchdesigner), as used in this course with TouchDesigner 2025.33070.

## What arrives

- **21 landmarks per hand,** up to two hands: the wrist, and the MCP (base knuckle), PIP (middle), DIP (near the tip) and TIP of each finger.
- The landmarks arrive **already named, as ordinary CHOP channels**, one per landmark and axis:

  ```
  h1:index_finger_tip:x
  h1:index_finger_tip:y
  h1:index_finger_tip:z
  h1:thumb_tip:x   ...
  ```

- The axis components are **`x`, `y`, `z`**, not `tx`, `ty`, `tz`. Several TouchDesigner operators (Join CHOP, CHOP to SOP) expect `tx ty tz`, so rename in the Select CHOP (Rename To `t*`).
- Values are **normalized 0–1, origin at the top-left, y counting downward.** Remap before driving anything in 3D. For a 2 × 2 SOP space centred on the origin: Scale 2, −2, 1 and Translate −1, 1, 0 in a Transform SOP. For the −4…4 particle grid: a Math CHOP from 0→1 to −4→4.
- Plugin versions differ. If your channel names do not match these, use the names in your own channel list.

## `h1` and `h2` are slots, not hands

`h1` is the **first hand detected**, `h2` the second. Lower one hand and raise the other and `h1` now refers to a different physical hand. To know which hand is which, read the handedness value, not the slot.

## Handedness reads backwards

The camera image is **mirrored** before MediaPipe sees it, so movement feels natural. Handedness therefore describes the mirrored picture: raise your actual right hand and it reads **"Left"**. That matches what you see on screen; it is correct, just surprising.

The mirror also explains the two most common "bugs" when mapping hand position: moving right sends something left (flip the sign of x), and moving up sends it down (flip the sign of y).

## When tracking stops

| Symptom | Cause |
|---|---|
| No channels at all | MediaPipe still starting (allow a full minute on first launch), or another app holding the camera |
| Channels frozen | Cooking switched off on the component, or tracking lost the hand |
| Values jump around | Poor light or busy background. A Lag CHOP after the Select smooths what remains. |
