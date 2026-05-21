# Set Volume

**Type:** Action  
**Category:** AUDIO

Changes the volume of a playing sound by retrieving its handle from `globalDict`.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Handle Key | String | `"sound"` | Handle key in `globalDict` |
| Volume | Float | `1.0` | New volume [0.0 – 1.0] |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |
| Volume | Input | Data (Float) |

## Generated code

```python
_aud_hdl = Range.logic.globalDict.get('_aud_bgm', None)
if _aud_hdl:
    _aud_hdl.volume = 0.5
```

## Typical usage

### Lower volume in a quiet zone

```
[On Collision: Property="quiet_zone"] → [Set Volume: Handle Key="bgm", Volume=0.2]
```

### Manual fade out with timer

```
[On Update] → [BTCustomTask:
                   t = {Get State Time: FSM ID="fade"}
                   vol = max(0.0, 1.0 - t / 3.0)
              ] → [Set Volume: Handle Key="bgm", Volume={vol}]
```

## Notes

- For automatic fades, use [Music Manager](music-manager.md) which implements fade with `deltaTime()` built in.
- Changing a handle's volume does not affect other handles even if they use the same file.
