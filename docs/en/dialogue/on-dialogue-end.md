# On Dialogue End

**Type:** Action (branch)  
**Category:** DIALOGUE

Fires for exactly **one frame** when a dialogue transitions from active to inactive (last line played). Must be on the same object as the corresponding [Dialogue Player](dialogue-player.md).

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Dialogue ID | String | `"dlg1"` | Must match the Dialogue Player |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| On End | Output | Exec |
| Otherwise | Output | Exec |

## Generated code

```python
_dlge_cur  = getattr(self, '_dlg_active_dlg1', False)
_dlge_prev = getattr(self, '_dlg_was_active_dlg1', False)
self._dlg_was_active_dlg1 = _dlge_cur
if not _dlge_cur and _dlge_prev:
    # On End branch
else:
    # Otherwise branch
```

## Typical usage

### Close HUD when dialogue ends

```
[On Update] → [On Dialogue End: Dialogue ID="intro"]
                  └── On End ──► [Overlay Scene: Scene=DialogueHUD, Mode=Remove]
                               ──► [Send Message: Subject="resume_gameplay"]
                  └── Otherwise ──► [...]
```

### Chain two dialogues

```
[On Update] → [On Dialogue End: Dialogue ID="part1"]
                  └── On End ──► [Start Dialogue: Dialogue ID="part2"]
                  └── Otherwise ──► [...]
```

## Notes

- Detects the falling edge of `_dlg_active` — fires only on the transition, not while dialogue is idle.
- Does not fire if dialogue was interrupted by [Stop Dialogue](stop-dialogue.md) (which clears state directly).
- Must be connected to `On Update` to run every frame.
