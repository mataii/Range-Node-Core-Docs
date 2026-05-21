# Set Bar

**Type:** Action  
**Category:** UI

Scales an object on the X axis in proportion to a 0.0–1.0 value. Designed for progress bars such as health, energy, or experience.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Bar Object | Enum | — | Object that represents the bar |
| Max Scale X | Float | `1.0` | Local X scale when the bar is at 100% |
| Value | Float | `1.0` | Fill percentage (0.0 – 1.0, used when socket is not connected) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Value | Input | Data (Float, 0.0 – 1.0) |
| Out | Output | Exec |

## Generated code

```python
_bar = self.own.scene.objects.get('health_bar')
if _bar:
    _pct = max(0.0, min(1.0, float(0.75)))
    _sc = _bar.localScale
    _bar.localScale = [_pct * 1.0, _sc[1], _sc[2]]
```

## Typical usage

### Health bar

```
[On Message: Subject="take_damage"]
    → [Add To Property: prop="health", Value=-10]
    → [Set Bar:
           Bar Object=health_bar
           Value={Math: {Get Property: prop="health"} / 100.0}]
```

### Skill charge bar

```
[On Update]
    → [Set Bar:
           Bar Object=skill_bar
           Value={Math: {Get Property: prop="skill_charge"} / 50.0}]
```

## Notes

- Only modifies the X scale; Y and Z remain unchanged.
- Value is automatically clamped between `0.0` and `1.0`.
- `Max Scale X` must match the object's original scale when representing 100%.
- The bar object can be in the game scene or in a HUD overlay scene.
