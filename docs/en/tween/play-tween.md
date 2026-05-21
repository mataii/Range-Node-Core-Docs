# Play Tween

**Type:** Action  
**Category:** TWEEN

Starts an interpolation by writing its parameters to `globalDict`. Must be used together with a processor node such as [Tween Position](tween-position.md), [Tween Float](tween-float.md), or [Tween Color](tween-color.md).

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Tween ID | String | `"tween"` | Unique identifier for this interpolation |
| Duration | Float | `1.0` | Duration in seconds |
| Ease | Enum | `linear` | Easing function: `linear`, `ease_in`, `ease_out`, `ease_in_out`, `bounce` |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
Range.logic.globalDict['_rnc_tw_door'] = {
    'dur': 1.0,
    'ease': 'ease_out',
    't': 0.0,
    'start': Range.logic.getClockTime()
}
```

## Typical usage

### Open door

```
[On Key Press: Key=E, Mode=PRESSED]
    → [Play Tween: ID="door", Duration=1.5, Ease=ease_out]

[On Update] → [Tween Position: ID="door", From=[0,0,0], To=[0,0,3]]
                  └── True ──► [Set State: State="open"]
```

### Color flash on taking damage

```
[On Message: Subject="take_damage"]
    → [Play Tween: ID="hit_flash", Duration=0.3, Ease=ease_out]

[On Update] → [Tween Color: ID="hit_flash", From=[2,0,0,1], To=[1,1,1,1]]
```

## Notes

- `Play Tween` restarts the interpolation from 0 if called while one is already in progress with the same ID.
- Time is measured with `getClockTime()` — real-world time, not affected by game logic pause.
- Use a unique ID per simultaneous interpolation — if two objects need independent tweens, use different IDs.
