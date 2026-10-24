# Everything that fails silently

Each of these gives you **no error message at all**. Most produce a black frame, a frozen frame, or a patch that "does nothing".

| # | Failure | What you see | Fix |
|---|---|---|---|
| 1 | **A wrong or empty parameter path** ([address, not a wire](address-not-a-wire.md)) | Nothing happens | Check the field. Drag the node in instead of typing. |
| 2 | **Cooking switched off** | Frozen on the last frame; looks exactly like "still loading" | Right-click the node or component and turn Cooking back on |
| 3 | **Render flag off** (the bottom-*right* square) | Visible in the viewer, missing from the render | Turn it on for the SOP inside the Geometry COMP **and** for the COMP itself. New nodes inside a geo do not inherit it. |
| 4 | **A Geometry COMP's default contents** | Your work hidden behind a shape you did not make | Go inside and delete the default shape |
| 5 | **Feedback collapsing the resolution** | Everything becomes a tiny blurry square | A Feedback TOP starts at 128×128 and drags the chain down. Set the Composite's Output Resolution to Custom, matched to the node before it. |
| 6 | **A lit material with no light** | Pure black render | Use a Constant MAT, or add a Light COMP for PBR/Phong |
| 7 | **The camera points at nothing** | Black render | Pull the camera's Translate Z back |
| 8 | **Points too small to see** | Black render | Scale everything up temporarily to check the geometry exists |
| 9 | **Metaball radius smaller than the point field** | Particles ignore the force | A metaball's field is zero outside its radius; enlarge it, and check the Force SOP goes into the Particle SOP's second input |
| 10 | **Another app holding the camera** | Black Video Device In | Quit Zoom, Teams, browser calls and camera apps, then restart TouchDesigner |
| 11 | **A Math CHOP range that does not match the data** | The target barely moves, or flies off screen | Read the real minimum and maximum in the viewer and put them in From Range |

## The method that solves all of them

**Click through your chain node by node and check what each one actually outputs:** the image, the resolution, the numbers in the graph. The place where it stops making sense is the place to fix.

A black render is not an error. An empty or unlit scene is a legitimate thing to want, so TouchDesigner stays quiet. Silence means it did exactly what you asked.
