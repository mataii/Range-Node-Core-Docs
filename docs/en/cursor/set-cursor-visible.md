# Set Cursor Visible

**Type:** Action  
**Category:** CURSOR

Shows, hides, or toggles system cursor visibility using `Range.logic.mouse.visible`.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Mode | Enum | `TOGGLE` | `SHOW` = show, `HIDE` = hide, `TOGGLE` = switch |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
# SHOW
Range.logic.mouse.visible = True

# HIDE
Range.logic.mouse.visible = False

# TOGGLE
Range.logic.mouse.visible = not Range.logic.mouse.visible
```

## Typical usage

### Hide cursor on FPS game start

```
[On Start] → [Set Cursor Visible: Mode=HIDE]
```

### Show cursor when pause menu opens

```
[On Message: Subject="pause_open"] → [Set Cursor Visible: Mode=SHOW]
[On Message: Subject="pause_close"] → [Set Cursor Visible: Mode=HIDE]
```

## Notes

- Hiding the cursor does not stop it from moving — it is still tracked by the system.
- To capture mouse movement while keeping the cursor hidden, combine this node with [Capture Mouse](capture-mouse.md).
