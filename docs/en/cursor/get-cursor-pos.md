# Get Cursor Pos

**Type:** Data  
**Category:** CURSOR

Returns the current mouse cursor position. Defaults to normalized [0.0, 1.0] coordinates; optionally remapped to [-1.0, 1.0].

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Axis | Enum | `X` | `X` = horizontal, `Y` = vertical |
| Remap | Bool | `False` | If True, converts from [0,1] to [-1,1] |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Value | Output | Data (Float) |

## Generated code

```python
# Axis=X, Remap=False
Range.logic.mouse.position[0]

# Axis=X, Remap=True
Range.logic.mouse.position[0] * 2.0 - 1.0
```

## Typical usage

### Detect if cursor is on the right half of the screen

```
[On Update] → [BT Condition: {Get Cursor Pos: Axis=X} > 0.5]
                  └── True ──► [...]
```

### Pass cursor position to a UI shader

```
[On Update] → [BTCustomTask:
                   x = {Get Cursor Pos: Axis=X, Remap=True}
                   y = {Get Cursor Pos: Axis=Y, Remap=True}
                   self.own['cursor_x'] = x
                   self.own['cursor_y'] = y]
```

## Notes

- `mouse.position` is a `(x, y)` tuple where `(0,0)` is the top-left corner and `(1,1)` is the bottom-right.
- Read-only — does not move the cursor.
