# Weapon Recoil

**Type:** Exec (In → Out)  
**Category:** PLAYER

Accumulates recoil per shot and recovers it gradually. Publishes pitch and yaw values for camera or viewmodel nodes to apply the visual effect. **Does not rotate anything directly** — only publishes data.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Pitch | Float | `3.0` | Vertical recoil per shot (degrees) |
| Max Pitch | Float | `15.0` | Maximum accumulated pitch (degrees) |
| Recovery Speed | Float | `8.0` | Recovery speed (degrees/s) |
| Random Yaw | Bool | `True` | Random horizontal recoil per shot |
| Yaw Spread | Float | `1.0` | Maximum random yaw per shot (degrees) |
| Max Yaw | Float | `5.0` | Maximum accumulated yaw in each direction (degrees) |
| Yaw Pattern | Bool | `False` | Uses a deterministic yaw pattern |
| Pattern | String | `"0.1,0.2,-0.1,0.3,-0.2"` | Comma-separated yaw values (degrees), cyclic |
| Reset Delay | Float | `0.0` | Seconds without firing before resetting pattern index (0 = disabled) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published variables

| Variable | Type | Description |
|----------|------|-------------|
| `weapon_recoil_pitch` | float | Accumulated pitch (degrees) |
| `weapon_recoil_yaw` | float | Accumulated yaw (degrees) |
| `weapon_recoil_offset` | float | `abs(pitch) + abs(yaw)` — total recoil magnitude |
| `weapon_recoil_amount` | float | Alias for `weapon_recoil_offset` |
| `weapon_recoil_active` | bool | `True` when recoil > 0.001 |

## Yaw modes

| Mode | Description |
|------|-------------|
| No yaw | Vertical recoil only |
| Random Yaw | Random value between `±yaw_spread` per shot |
| Yaw Pattern | Deterministic cyclic sequence (same pattern each magazine) |

## Weapon Balancer integration

Scales recoil by `wb_recoil` (published by Weapon Balancer). If not present, the multiplier is 1.0.

## Weapon ADS integration

The published pitch and yaw are raw data. To make ADS visually reduce recoil, multiply `weapon_recoil_pitch/yaw` by `weapon_ads_recoil_mult` in the node that applies the camera rotation.

## Graph position

```
[Weapon Fire Executor] → [Weapon Recoil] → (camera / viewmodel consumer)
```
