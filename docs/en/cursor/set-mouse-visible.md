# Set Mouse Visible

**Type:** Action  
**Category:** CURSOR

Directly sets mouse cursor visibility. Simplified version of [Set Cursor Visible](set-cursor-visible.md) that accepts a boolean value.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Visible | Bool | `True` | `True` = show cursor, `False` = hide |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |
| Visible | Input | Data (Bool) |

## Generated code

```python
Range.logic.mouse.visible = True
```

## Notes

- Equivalent to `Set Cursor Visible` with mode `SHOW` or `HIDE`, but accepts a boolean data value instead of an enum. Useful when the visibility value comes from a variable.
- For toggling (TOGGLE mode), use [Set Cursor Visible](set-cursor-visible.md) with mode `TOGGLE`.
