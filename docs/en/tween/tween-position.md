# Tween Position

**Type:** Processor / Branch (True / False)  
**Category:** TWEEN

Interpolates the object's `worldPosition` from `From` to `To` using the tween parameters identified by `ID`. Branches to `True` when complete.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Tween ID | String | `"tween"` | Must match the ID used in `Play Tween` |
| From | Vector | `[0,0,0]` | Start position |
| To | Vector | `[0,0,1]` | End position |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| True | Output | Exec (tween complete) |
| False | Output | Exec (tween in progress) |

## Generated code (simplified)

```python
_tw = Range.logic.globalDict.get('_rnc_tw_door', None)
if _tw:
    _tw_t = min(1.0, (Range.logic.getClockTime() - _tw['start']) / _tw['dur'])
    _tw_e = ease_function(_tw_t, _tw['ease'])  # apply easing
    _tw_pos = [_from[i] + (_to[i] - _from[i]) * _tw_e for i in range(3)]
    self.own.worldPosition = _tw_pos
    if _tw_t >= 1.0:
        #TRUE_PATH#
    else:
        #FALSE_PATH#
```

## Typical usage

### Rising door

```
# On entering "opening" state:
[On State Enter: FSM ID="door", State="opening"]
    └── On Enter ──► [Play Tween: ID="door", Duration=1.5, Ease=ease_out]

# Every frame:
[On Update] → [On State: FSM ID="door", State="opening"]
                  └── True ──► [Tween Position: ID="door", From=[0,0,0], To=[0,0,3]]
                                    └── True ──► [Set State: State="open"]
```

### Smooth teleport

```
[On Message: Subject="teleport_to_B"]
    → [Play Tween: ID="tp", Duration=0.5, Ease=ease_in_out]

[On Update] → [Tween Position: ID="tp",
                               From={Object Position: Object="point_A"}
                               To={Object Position: Object="point_B"}]
```

## Notes

- If `globalDict['_rnc_tw_<id>']` doesn't exist, the node falls to `False` without error.
- `From` and `To` vectors can be fixed coordinates or connected from data sockets.
