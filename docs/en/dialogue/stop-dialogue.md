# Stop Dialogue

**Type:** Action  
**Category:** DIALOGUE

Force-stops an active dialogue and clears pending signals from `globalDict`. Useful for interrupting a dialogue from an external event.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Dialogue ID | String | `"dlg1"` | Must match the Dialogue Player to stop |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
if hasattr(self, '_dlg_active_dlg1'):
    self._dlg_active_dlg1 = False
Range.logic.globalDict.pop('_dlg_start_dlg1', None)
Range.logic.globalDict.pop('_dlg_adv_dlg1',   None)
```

## Typical usage

### Interrupt dialogue on damage

```
[On Message: Subject="take_damage"]
    → [Stop Dialogue: Dialogue ID="intro"]
    → [Overlay Scene: Scene=DialogueHUD, Mode=Remove]
```

### Cancel dialogue on Escape

```
[On Key Press: Key=ESC, Mode=PRESSED]
    → [Stop Dialogue: Dialogue ID="intro"]
```

## Notes

- `Stop Dialogue` sets `_dlg_active` to `False` directly — [On Dialogue End](on-dialogue-end.md) **does not** fire in this case, because the normal active → inactive transition through the Player does not occur.
- For cleanup logic, wire it explicitly after `Stop Dialogue`.
