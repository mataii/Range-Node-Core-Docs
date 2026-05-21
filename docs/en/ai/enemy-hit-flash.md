# Enemy Hit Flash

**Type:** Exec (In → Out)  
**Category:** AI  
**Execution Order:** 62 (after Enemy Animation)

Applies a color flash effect to the object when it takes damage. The base color is captured automatically and restored when the effect expires.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Duration | Float (≥0.01) | `0.15` | Flash duration in seconds |
| Strength | Float (≥0.0) | `1.0` | Effect intensity (adds to R, subtracts from G and B) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Variables read

| Variable | Source |
|----------|--------|
| `ai_took_damage` | Damage Receiver |

## Behavior

### Each frame

1. Decrements `_ehf_timer`.
2. If `ai_took_damage = True`: sets `_ehf_timer = duration`.
3. If `_ehf_timer ≤ 0`: captures the object's base color (`self.own.color`).
4. Computes the modified color:
   ```python
   color = [base[0] + strength, base[1] - strength, base[2] - strength, base[3]]
   color = [clamp(r, 0, 1), clamp(g, 0, 1), clamp(b, 0, 1), alpha]
   self.own.color = color
   ```

### Visual result

With `Strength = 1.0`:
- R rises to maximum → the object turns red
- G and B drop to minimum → result is pure red

The effect lasts `duration` seconds, then the color returns to the base.

### Base color

The base color is captured **when the timer is 0** (while the flash is inactive). If the object's material color changes during gameplay, the node captures the new value automatically.

## Typical usage

### Basic hit flash

```
Duration: 0.15
Strength: 1.0
```

```
[Damage Receiver] → ... → [Enemy Animation] → [Enemy Hit Flash]
```

### Subtle NPC flash

```
Duration: 0.3
Strength: 0.5
```

### Disable flash

Set `Strength = 0.0` or remove the node from the tree.

## Requirements

The object must have a material that responds to `self.own.color`. In Range Game Engine, EEVEE materials with Object Color enabled receive the value. Materials with hardcoded colors in the shader will ignore the change.

## Notes

- `_ehf_base_color` updates every frame when no flash is active — if the object has a color animation, the node captures it correctly.
- If `ai_took_damage` is True across multiple consecutive frames (accumulated damage), the timer resets each time, extending the effect.
- Enemy Hit Flash operates only on the object color (`KX_GameObject.color`), not on materials directly.
