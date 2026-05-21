# Weapon ADS

**Type:** Exec (In → Out)  
**Category:** PLAYER

Implements the Aim Down Sights system. Manages the smooth transition in/out of ADS mode and publishes multipliers for FOV, recoil, spread, and sensitivity that other pipeline nodes consume.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Right Mouse to Aim | Bool | `True` | Right mouse button as ADS input |
| Toggle Mode | Bool | `False` | Press to enter/exit (instead of hold) |
| Instant ADS | Bool | `False` | Instant transition with no smoothing |
| ADS Speed | Float | `8.0` | Transition speed (alpha/s) |
| FOV Mult | Float (0.1–1.0) | `0.65` | FOV multiplier at full ADS (< 1 = zoom) |
| Recoil Mult | Float (0–1) | `0.5` | Multiplies recoil while aiming |
| Spread Mult | Float (0–1) | `0.3` | Multiplies spread while aiming |
| Sensitivity Mult | Float (0.1–1) | `0.7` | Multiplies mouse sensitivity while aiming |
| Movement Slowdown | Bool | `False` | Publishes `weapon_ads_move_mult` to slow the player |
| Move Mult | Float (0.1–1) | `0.75` | Movement speed factor while in ADS |
| Camera Offset | Bool | `False` | Publishes `weapon_ads_camera_offset` (camera Z offset) |
| Camera Offset | Float | `0.05` | Camera Z offset at full ADS |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published variables

| Variable | Type | Description |
|----------|------|-------------|
| `weapon_ads` | bool | `True` when ADS input is active |
| `weapon_ads_active` | bool | Alias for `weapon_ads` |
| `weapon_ads_alpha` | float (0–1) | ADS transition progress (0 = hip, 1 = full ADS) |
| `weapon_ads_fov_mult` | float | Interpolated FOV multiplier |
| `weapon_ads_recoil_mult` | float | Interpolated recoil multiplier |
| `weapon_ads_spread_mult` | float | Interpolated spread multiplier |
| `weapon_ads_sensitivity_mult` | float | Interpolated sensitivity multiplier |
| `weapon_ads_move_mult` | float | Speed multiplier (only if `Movement Slowdown = True`) |
| `weapon_ads_camera_offset` | float | Camera offset (only if `Camera Offset = True`) |

## Integration with other nodes

- **Weapon Recoil** reads `weapon_ads_recoil_mult` to scale recoil.
- **Weapon Spread** reads `weapon_ads_spread_mult` when `ADS Bonus` is enabled.
- **Weapon Animation** uses `weapon_ads_active` for ADS enter/exit animations.
- **Weapon FX System** suppresses muzzle flash when `Suppress in ADS` is enabled.

## Graph position

```
[Weapon State Publisher] → [Weapon Reload] → [Weapon ADS] → [Weapon Fire Executor] → ...
```
