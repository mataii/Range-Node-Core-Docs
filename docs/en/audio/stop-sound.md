# Stop Sound

**Type:** Action  
**Category:** AUDIO

Stops a playing sound by retrieving its handle from `globalDict` and calling `handle.stop()`.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Handle Key | String | `"sound"` | Key used in `Play Sound` for this sound |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
_aud_hdl = Range.logic.globalDict.get('_aud_bgm', None)
if _aud_hdl:
    _aud_hdl.stop()
```

## Typical usage

### Stop music when switching scenes

```
[On Key Press: Key=ESC, Mode=PRESSED]
    → [Stop Sound: Handle Key="bgm"]
    → [Scene Switch: Scene="MainMenu"]
```

### Stop looping sound effect

```
[On State Exit: FSM ID="engine", State="running"]
    └── On Exit ──► [Stop Sound: Handle Key="engine_loop"]
```

## Notes

- If the key doesn't exist in `globalDict` or the handle has already expired, the call is safely ignored (no error thrown).
- After `stop()`, the handle is invalid — do not attempt `pause()` or `resume()` afterward.
