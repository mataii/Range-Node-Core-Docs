# Advance Dialogue

**Type:** Action  
**Category:** DIALOGUE

Manually advances to the next line of an active dialogue. Equivalent to the player pressing the advance key, but can be triggered by any event.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Dialogue ID | String | `"dlg1"` | Must match the corresponding Dialogue Player |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
Range.logic.globalDict['_dlg_adv_dlg1'] = True
```

## Typical usage

### Advance on click anywhere on screen

```
# Dialogue Player with Advance=Manual Only
[On Mouse Button: Button=LEFT, Mode=PRESSED]
    → [Advance Dialogue: Dialogue ID="intro"]
```

### Advance automatically when animation ends

```
[On Message: Subject="anim_done"]
    → [Advance Dialogue: Dialogue ID="cutscene"]
```

## Notes

- Works whether `Dialogue Player` has `Advance=Manual Only` or any other mode (it is combined with the configured key).
- If the dialogue is not active, the signal is silently consumed (`globalDict.pop` has no effect).
- Combine with [Stop Dialogue](stop-dialogue.md) and [On Dialogue End](on-dialogue-end.md) for full flow control.
