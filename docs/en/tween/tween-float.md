# Tween Float

**Type:** Processor / Branch (True / False)  
**Category:** TWEEN

Interpolates a float BGE property on the object from `From` to `To` using the tween parameters identified by `ID`.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Tween ID | String | `"tween"` | Must match the ID used in `Play Tween` |
| Property | String | `"value"` | BGE property name to animate |
| From | Float | `0.0` | Start value |
| To | Float | `1.0` | End value |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| True | Output | Exec (complete) |
| False | Output | Exec (in progress) |

## Generated code (simplified)

```python
_tw = Range.logic.globalDict.get('_rnc_tw_hp', None)
if _tw:
    _tw_t = min(1.0, (Range.logic.getClockTime() - _tw['start']) / _tw['dur'])
    _tw_e = ease_function(_tw_t, _tw['ease'])
    self.own['hp_bar'] = 0.0 + (1.0 - 0.0) * _tw_e
    if _tw_t >= 1.0:
        #TRUE_PATH#
```

## Typical usage

### Animate health bar on damage

```
[On Message: Subject="take_damage"]
    → [BTCustomTask: new_hp = max(0, self.own.get('hp',100) - 10)]
    → [Play Tween: ID="hp_tween", Duration=0.5, Ease=ease_out]
    → [Set Property: prop="hp", Value={new_hp}]

[On Update] → [Tween Float:
                  ID = "hp_tween"
                  Property = "hp_visual"
                  From = {Get Property: prop="hp_visual"}
                  To = {Get Property: prop="hp"}]
              → [Set Bar: Value={hp_visual}]
```

### Volume fade

```
[On State Enter: FSM ID="game", State="quiet_zone"]
    └── On Enter ──► [Play Tween: ID="vol_fade", Duration=2.0, Ease=ease_in_out]

[On Update] → [Tween Float: ID="vol_fade", Property="vol_prop", From=1.0, To=0.2]
              → [Set Volume: Handle Key="bgm", Volume={vol_prop}]
```

## Notes

- The BGE property is updated directly every frame the tween is active.
- The node does not read the property's current value as `From` — you must specify it explicitly or connect it from a data socket.
