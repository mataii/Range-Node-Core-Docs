# Tween Color

**Type:** Processor / Branch (True / False)  
**Category:** TWEEN

Interpolates the object's RGBA color (`self.own.color`) from `From` to `To` using the specified tween parameters.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Tween ID | String | `"tween"` | Must match the ID used in `Play Tween` |
| From | Vector (RGBA) | `[1,1,1,1]` | Start color [R, G, B, A] |
| To | Vector (RGBA) | `[1,0,0,1]` | End color [R, G, B, A] |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| True | Output | Exec (complete) |
| False | Output | Exec (in progress) |

## Generated code (simplified)

```python
_tw = Range.logic.globalDict.get('_rnc_tw_flash', None)
if _tw:
    _tw_t = min(1.0, (Range.logic.getClockTime() - _tw['start']) / _tw['dur'])
    _tw_e = ease_function(_tw_t, _tw['ease'])
    _tw_col = [_from[i] + (_to[i] - _from[i]) * _tw_e for i in range(4)]
    self.own.color = _tw_col
    if _tw_t >= 1.0:
        #TRUE_PATH#
```

## Typical usage

### Red flash on taking damage

```
[On Message: Subject="take_damage"]
    → [Play Tween: ID="hit_flash", Duration=0.3, Ease=ease_out]

[On Update] → [Tween Color:
                  ID = "hit_flash"
                  From = [2.0, 0.0, 0.0, 1.0]  # bright red
                  To = [1.0, 1.0, 1.0, 1.0]]   # normal color
```

### Alpha fade

```
[On State Enter: FSM ID="player", State="invisible"]
    └── On Enter ──► [Play Tween: ID="fade_out", Duration=1.0, Ease=ease_in]

[On Update] → [Tween Color:
                  ID = "fade_out"
                  From = [1.0, 1.0, 1.0, 1.0]
                  To = [1.0, 1.0, 1.0, 0.0]]
```

## Notes

- `self.own.color` accepts values >1.0 for "glow" (emission) effects on materials that support it.
- The alpha channel controls transparency only if the object's material has transparency enabled.
- To colorize a different object, use `BTCustomTask: scene.objects['name'].color = [r,g,b,a]`.
