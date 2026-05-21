# CURSOR Category

Nodes for controlling the mouse cursor and capturing its movement for cameras or characters.

## Available Nodes

| Node | Type | Description |
|------|------|-------------|
| [Set Cursor Visible](set-cursor-visible.md) | Action | Shows, hides, or toggles cursor visibility |
| [Center Cursor](center-cursor.md) | Action | Moves the cursor to the screen center |
| [Capture Mouse](capture-mouse.md) | Action | Captures mouse movement each frame (for mouselook) |
| [Get Mouse Delta](get-mouse-delta.md) | Data | Returns the X or Y delta from a mouse capture |
| [Get Cursor Pos](get-cursor-pos.md) | Data | Returns the current cursor position |
| [Is Cursor Visible](is-cursor-visible.md) | Data | Returns whether the cursor is visible |
| [Cursor Over](cursor-over.md) | Branch | Detects whether the cursor is over a 3D object |
| [Set Mouse Visible](set-mouse-visible.md) | Action | Alias of Set Cursor Visible (mouse_nodes module) |
| [Set Mouse Position](set-mouse-position.md) | Action | Moves the cursor to a specific position |

## Mouse Capture Pattern

For mouselook, `Capture Mouse` computes the per-frame delta and stores it in object attributes. Then `Get Mouse Delta` retrieves those values to apply to camera or character rotation.

```
[On Update] → [Capture Mouse: Capture ID="cam", Sensitivity=0.3]
[On Update] → [BTCustomTask:
                   dx = {Get Mouse Delta: Capture ID="cam", Axis=X}
                   dy = {Get Mouse Delta: Capture ID="cam", Axis=Y}
                   self.own.applyRotation([0, 0, -dx], False)]
```
