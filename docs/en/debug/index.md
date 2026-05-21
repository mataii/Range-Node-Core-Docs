# DEBUG Category

Diagnostic and runtime visualization nodes. Useful during development for inspecting values, tracing paths, and verifying geometry without external tools.

## Available Nodes

| Node | Type | Description |
|------|------|-------------|
| [Print](print.md) | Action | Prints a message to the console |
| [Draw Line](draw-line.md) | Action | Draws a 3D line for one frame |
| [Draw Ray](draw-ray.md) | Action | Draws a ray along a local axis of the object |

## Notes

- All nodes in this category are development-only. They have no effect on game logic.
- `Draw Line` and `Draw Ray` are only visible on the frame they execute — call them every frame (e.g. from `On Update`) to see a continuous line.
- Drawn lines do not appear in renders or in-game screenshots.
