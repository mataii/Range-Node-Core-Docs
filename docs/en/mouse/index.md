# MOUSE Category

Nodes for reading mouse button state, scroll wheel, and cursor position. Complement the [CURSOR](../cursor/index.md) category.

## Available Nodes

| Node | Type | Description |
|------|------|-------------|
| [Mouse Button](mouse-button.md) | Branch | Detects left, right, or middle click |
| [Mouse Scroll](mouse-scroll.md) | Branch | Detects scroll wheel up or down |
| [Mouse Position](mouse-position.md) | Data | Returns cursor X or Y position |
| [Mouse Over](mouse-over.md) | Branch | Detects whether cursor is over a 3D object |
| [Mouse Raycast](mouse-raycast.md) | Branch | Ray from camera to cursor with hit result |

## Difference between Mouse Over and Cursor Over

Both nodes cast a ray from the camera to the cursor, but `Mouse Over` (in `mouse_nodes.py`) uses `getScreenRay` directly and exposes the hit object as a result. `Cursor Over` (in `cursor_nodes.py`) uses a similar approach but allows filtering by BGE property or specific object name.
