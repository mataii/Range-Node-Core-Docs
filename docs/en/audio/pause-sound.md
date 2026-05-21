# Pause Sound

**Type:** Action  
**Category:** AUDIO

Pauses a playing sound. The handle remains valid — it can be resumed with [Resume Sound](resume-sound.md).

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Handle Key | String | `"sound"` | Handle key in `globalDict` |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
_aud_hdl = Range.logic.globalDict.get('_aud_bgm', None)
if _aud_hdl:
    _aud_hdl.pause()
```

## Typical usage

### Pause music when opening menu

```
[On Message: Subject="pause_open"] → [Pause Sound: Handle Key="bgm"]
[On Message: Subject="pause_close"] → [Resume Sound: Handle Key="bgm"]
```

## Notes

- `pause()` freezes playback at the current position.
- If the sound has already finished, `pause()` has no effect (the handle is invalid).
