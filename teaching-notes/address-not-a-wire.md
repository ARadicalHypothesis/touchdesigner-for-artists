# Address, not a wire

TouchDesigner has two ways of connecting things, and beginners only see one of them.

- **Wires** carry data from one node into the next, left to right. You can see them in the network.
- **Addresses** are assignments: "use *that* node for this setting". You drag a node onto a parameter field, or type its path or an expression. **No cable appears.**

The course says "address, not a wire" every time one comes up, because a wrong or empty address **fails silently**. There is no error, and nothing in the network editor shows that the link is missing. When something "does nothing", check the addresses first.

## Where they appear in this course

| Session | Where | Field | Typical content |
|---|---|---|---|
| 1 | Any node | Any parameter | Try it: drag a node onto another node's parameter and watch the field fill in |
| 2 | CHOP to SOP | CHOP | `../join1` |
| 2 | Geometry COMP | Material | `constant1` |
| 2, 3, 4 | Render TOP | Camera, Geometry, Lights | `cam1`, `geo1`, `light1` |
| 3 | Metaball SOP | Center X, Center Z | `op('null_mouse')['tx']`, `op('null_mouse')['ty']` |
| 4 | Metaball SOP | Center X, Center Z | `op('null')['x']`, `op('null')['y']` |
| 4 | Geometry COMP (instancing) | Instance source, Number of instances | `inst_positions`, `op('inst_positions').numSamples` |
| 4 | Feedback TOP | Target TOP | `trail_comp` |

## Three mechanisms that look alike

- **Expression:** the field turns blue and evaluates live.
- **CHOP export:** the field turns green. It needs the source CHOP's export flag on; an unfinished export stays green and reads zero.
- **Export table:** a Table DAT listing channel, target node and parameter.

## Practical rules

- **Drag rather than type** into the Render TOP's Camera, Geometry and Lights fields. In build 2025.33070, typing a relative path there can look accepted and silently stay empty.
- There is no "copy path" shortcut in that build, so dragging is also the fastest way to get a path right.
- Point addresses at **named Nulls**, not at working nodes. Then you can rebuild everything behind the Null and the address still holds.
