# Set Mouse Position

**Type:** Action  
**Category:** CURSOR

Moves the mouse cursor to a specific screen position using normalized [0.0, 1.0] coordinates.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| X | Float | `0.5` | Horizontal position [0.0 = left, 1.0 = right] |
| Y | Float | `0.5` | Vertical position [0.0 = top, 1.0 = bottom] |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |
| X | Input | Data (Float) |
| Y | Input | Data (Float) |

## Generated code

```python
Range.logic.mouse.position = (0.5, 0.5)
```

## Typical usage

### Move cursor to a UI element

```
[On Message: Subject="show_menu"] → [Set Mouse Position: X=0.5, Y=0.3]
                                   → [Set Cursor Visible: Mode=SHOW]
```

### Teleport cursor to center when opening inventory

```
[On Key Press: Key=TAB, Mode=PRESSED]
    → [Set Mouse Position: X=0.5, Y=0.5]
    → [Set Cursor Visible: Mode=SHOW]
```

## Notes

- Direct equivalent of `Center Cursor` when using `X=0.5, Y=0.5`, but allows arbitrary positions.
- The OS may ignore the position change if the window does not have focus.
