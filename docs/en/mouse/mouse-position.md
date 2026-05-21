# Mouse Position

**Type:** Data  
**Category:** MOUSE

Returns the X or Y position of the mouse cursor in normalized [0.0, 1.0] coordinates.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Axis | Enum | `X` | `X` = horizontal, `Y` = vertical |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Value | Output | Data (Float) |

## Generated code

```python
# Axis = X (stored in local variable _ms_x)
_ms_x = Range.logic.mouse.position[0]
# then the expression returns _ms_x

# Axis = Y
_ms_y = Range.logic.mouse.position[1]
```

## Typical usage

### Control a parameter with cursor horizontal position

```
[On Update] → [Set Light Energy:
                   Energy = {Mouse Position: Axis=X}]
```

### Debug cursor position

```
[On Update] → [Print: Message = {Mouse Position: Axis=X}]
```

## Notes

- Identical to [Get Cursor Pos](../cursor/get-cursor-pos.md) but without the remap option. Use `Get Cursor Pos` if you need the [-1, 1] range.
- `(0,0)` is the top-left corner, `(1,1)` is the bottom-right corner.
