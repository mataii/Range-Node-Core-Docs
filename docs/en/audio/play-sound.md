# Play Sound

**Type:** Action  
**Category:** AUDIO

Plays an audio file using the `aud` module. Stores the playback handle in `globalDict` for later control.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Sound File | String | `""` | Path to the audio file (relative to blend or absolute) |
| Handle Key | String | `"sound"` | Key in `globalDict` to store the handle |
| Volume | Float | `1.0` | Initial volume [0.0 – 1.0] |
| Pitch | Float | `1.0` | Pitch (1.0 = normal) |
| Pan | Float | `0.0` | Stereo pan [-1.0 = left, 1.0 = right] |
| Loop | Bool | `False` | If True, loops indefinitely |
| 3D | Bool | `False` | If True, enables positional 3D audio |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |
| Volume | Input | Data (Float) |
| Pitch | Input | Data (Float) |

## Generated code

```python
import aud
_aud_dev = aud.Device()
_aud_snd = aud.Sound.file('explosion.wav')
_aud_hdl = _aud_dev.play(_aud_snd)
_aud_hdl.volume = 1.0
_aud_hdl.pitch  = 1.0
_aud_hdl.loop   = -1 if True else 0  # Loop
Range.logic.globalDict['_aud_bgm'] = _aud_hdl
```

For 3D audio, object position and orientation are added.

## Typical usage

### Looping background music

```
[On Start] → [Play Sound:
                 Sound File = "music/theme.wav"
                 Handle Key = "bgm"
                 Volume = 0.8
                 Loop = True]
```

### Gunshot sound effect

```
[Weapon Fire Executor] → [Play Sound:
                              Sound File = "sfx/gunshot.wav"
                              Handle Key = "shot"
                              Pitch = {Math: random(0.9, 1.1)}]
```

## Notes

- The file path is relative to the blend directory. Use `//sfx/file.wav` for relative paths.
- If `Loop = True`, the handle stays active indefinitely — use `Stop Sound` to stop it.
- Each call creates a new handle. If you use the same key twice without stopping the previous one, the old handle becomes orphaned.
