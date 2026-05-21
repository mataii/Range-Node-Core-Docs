# Is Cursor Visible

**Type:** Data  
**Category:** CURSOR

Returns whether the mouse cursor is currently visible.

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Visible | Output | Data (Bool) |

## Generated code

```python
Range.logic.mouse.visible
```

## Typical usage

### Condition logic on cursor state

```
[On Update] → [BT Condition: {Is Cursor Visible}]
                  └── True ──► [...]  # cursor visible: UI mode active
```

### Save state before changing

```
[On Start] → [BTCustomTask:
                  self._bt_bb['was_cursor_visible'] = {Is Cursor Visible}]
```

## Notes

- Data-only node — no Exec sockets.
- Reads `Range.logic.mouse.visible` directly — a system boolean value.
