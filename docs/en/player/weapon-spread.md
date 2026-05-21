# Weapon Spread

**Type:** Exec (In → Out)  
**Category:** PLAYER

Manages the weapon's ballistic spread cone. Accumulates bloom per shot and recovers it gradually. Publishes pitch/yaw offsets per fire frame that **Hit Scan Weapon** and **Weapon Projectile** consume to deflect the ray or projectile.

## Properties

### Base spread

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Base Spread | Float | `1.0` | Minimum inaccuracy cone (degrees) |
| Max Spread | Float | `8.0` | Maximum accumulated bloom (degrees) |
| Per Shot | Float | `2.0` | Bloom added per shot (degrees) |
| Recovery Speed | Float | `6.0` | Recovery speed toward Base Spread (degrees/s) |

### Penalties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Moving Penalty | Bool | `True` | Increases spread while moving |
| Move Multiplier | Float | `2.0` | Spread multiplier while moving |
| Airborne Penalty | Bool | `True` | Increases spread while airborne |
| Air Multiplier | Float | `3.0` | Spread multiplier while airborne |
| Crouch Bonus | Bool | `False` | Reduces spread while crouching |
| Crouch Multiplier | Float (0.1–1) | `0.6` | Spread multiplier while crouching (< 1 = more accurate) |
| ADS Bonus | Bool | `True` | Applies `weapon_ads_spread_mult` from Weapon ADS |

### Recoil coupling

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Recoil Coupling | Bool | `False` | Adds a fraction of recoil to bloom per shot |
| Recoil Scale | Float | `0.5` | Fraction of `weapon_recoil_offset` added to bloom |

### Deterministic pattern

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Pattern Spread | Bool | `False` | Uses a fixed magnitude list instead of bloom |
| Pattern | String | `"1.0,2.0,1.5,2.5,1.0"` | Comma-separated cone values (degrees), cyclic |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published variables

| Variable | Type | Description |
|----------|------|-------------|
| `weapon_spread_pitch` | float | Vertical shot deviation (radians). Non-zero only on fire frames. |
| `weapon_spread_yaw` | float | Horizontal shot deviation (radians). Non-zero only on fire frames. |
| `weapon_spread` | float | Current effective cone (radians). Useful for HUD. |
| `weapon_spread_multiplier` | float | Total multiplier applied to the base cone |
| `weapon_spread_active` | bool | `True` when bloom > Base Spread |
| `weapon_accuracy` | float (0–1) | `1.0` = maximum accuracy, `0.0` = maximum bloom |

## Graph position

```
[Weapon Fire Executor] → [Weapon Spread] → [Hit Scan Weapon]
                                         → [Weapon Projectile]
```
