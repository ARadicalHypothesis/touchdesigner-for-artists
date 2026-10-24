# Session 0 · Pre-flight setup

Finish all six steps **at least 72 hours before the first session**. The workshops are short, and none of that time can go on installers.

Slides: [slides-00-setup.pdf](slides-00-setup.pdf)

## Step 0 · Will your machine cope?

| | Minimum | Notes |
|---|---|---|
| Windows | Windows 10 or 11, 64-bit; 8 GB RAM (16 GB comfortable) | A dedicated NVIDIA GPU is strongly preferred. Integrated graphics will run Sessions 1–2 but may struggle with Session 3 particles. |
| macOS | Apple Silicon (M1 or newer) | Intel Macs need a discrete AMD GPU. Older Intel Macs with integrated graphics: tell your instructor now. |
| Everyone | A working webcam, about 3 GB of free disk, a room where you can wave your hands in reasonable light | The MediaPipe plugin alone is over 500 MB. |

## Step 1 · Install TouchDesigner

1. Go to [derivative.ca/download](https://derivative.ca/download) and choose the installer for your operating system.
2. Install build **2025.33070** and accept the defaults.
3. Open TouchDesigner once to check that it launches.

**Use the same build as everyone else.** The course files are made in 2025.33070. A different build may refuse to open them, and on a short course that is very hard to recover from. Do not install "the newest" instead. Derivative's [Getting Started guide](https://docs.derivative.ca/Getting_Started_With_TouchDesigner) covers the interface if anything is unclear.

## Step 2 · Get your free licence key

We use the free **Non-Commercial** licence. It costs nothing and does not expire.

1. Create a free account at [derivative.ca](https://derivative.ca). New accounts come with Non-Commercial keys.
2. Open TouchDesigner and sign in with that account in the licence window.
3. Select the Non-Commercial licence and click **Create Key**.

You are done when TouchDesigner opens with no licence pop-up. The one limit: Non-Commercial caps image resolution at 1280×1280. Everything in the course fits inside that, which is why the project runs at 1280×720. An Educational licence also exists, but you do not need it ([licensing details](https://docs.derivative.ca/Licensing)).

## Step 3 · Download the MediaPipe plugin

This is what lets TouchDesigner see your hands. There is nothing to install: it is a folder you unzip.

1. Open the [releases page](https://github.com/torinmb/mediapipe-touchdesigner/releases/latest).
2. Under **Assets**, download **`release.zip`**.
3. Unzip it somewhere permanent, such as a course folder on your Desktop, not Downloads.

> **Do not download "Source code (zip)".** That is the developer version and it will not work. This is the mistake almost everyone makes.

The plugin is free and open source, by Torin Blankensmith, and runs on Mac and PC. Its [README](https://github.com/torinmb/mediapipe-touchdesigner/blob/main/README.md) lists everything it can track.

## Step 4 · Run the MediaPipe example

1. Inside the unzipped folder, open `MediaPipe TouchDesigner.toe`.
2. **Wait.** The first launch starts a small local web server and loads the tracking models. Thirty seconds of loading screen is normal; do not force-quit.
3. Allow camera access if asked, open the hand tracking example and hold your hand up.

Success looks like dots and lines drawn over your hand, following it, and numbers changing when you open and close your fingers. Keep the folder together: do not move or rename `MediaPipe.tox`.

## Step 5 · Camera permissions

A blocked camera looks exactly like a broken install.

- **macOS:** System Settings → Privacy & Security → Camera → switch TouchDesigner on, then restart TouchDesigner.
- **Windows:** Settings → Privacy & security → Camera → allow camera access, including for desktop apps.

**Only one app can use the camera at a time.** Zoom, Teams, Meet, Photo Booth, the Windows Camera app or a browser tab on a call will lock TouchDesigner out, and TouchDesigner will show black with no explanation. If you join an online class on the same laptop, join the call from your phone or another device.

## Step 6 · Post your proof-of-life screenshots

Two screenshots, posted to the class channel **48 hours before Session 1**, with your name, operating system and TouchDesigner build number:

1. A TouchDesigner window showing your live webcam.
2. Live hand tracking: the dots on your hand, or the tracking numbers visibly changing.

If something is broken, post a screenshot of what you *do* see and the exact error text. A student who posts early gets fixed; one who waits loses the first session.

## If something goes wrong

| Symptom | Fix |
|---|---|
| Camera shows black | Another app has it. Quit Zoom, Teams, browser tabs and the Camera app, then restart TouchDesigner. |
| Camera dropdown is empty | Permissions (Step 5). On Windows, check that desktop apps are allowed. |
| MediaPipe never finishes loading | Give it a full minute. Then close it, make sure nothing else has the camera, and reopen. If it persists, you probably downloaded the source code instead of `release.zip`. |
| Everything is tiny and blurry | A resolution mismatch, a real TouchDesigner quirk. It is explained in Session 1. |
| TouchDesigner will not open a course file | Almost always a build mismatch. Check **Help → About** and compare with 2025.33070. |
| No webcam | Tell your instructor; a recorded tracking file lets you follow along identically. |

## Checklist

- [ ] TouchDesigner 2025.33070 installed and opening without a licence pop-up
- [ ] Free Non-Commercial licence key created and active
- [ ] `release.zip` downloaded from the MediaPipe releases page and unzipped somewhere permanent
- [ ] `MediaPipe TouchDesigner.toe` opens and tracks my hand
- [ ] Camera permissions granted, and no other app holding the camera
- [ ] Both screenshots posted with my name, OS and build number

Optional warm-up: [Derivative's documentation](https://docs.derivative.ca) and the [Getting Started guide](https://docs.derivative.ca/Getting_Started_With_TouchDesigner). Nothing there is assumed in Session 1; the course starts from zero.
