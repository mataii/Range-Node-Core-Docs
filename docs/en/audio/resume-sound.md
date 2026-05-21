# Resume Sound

**Type:** Action  
**Category:** AUDIO

Resumes a paused sound from where it was stopped.

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
    _aud_hdl.resume()
```

## Typical usage

### Resume music when closing pause menu

```
[On Message: Subject="pause_close"] → [Resume Sound: Handle Key="bgm"]
```

## Notes

- Only works if the sound was previously paused with [Pause Sound](pause-sound.md). If the handle is invalid or the sound has finished, has no effect.
