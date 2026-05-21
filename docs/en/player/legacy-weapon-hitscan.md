# Weapon Hitscan (Legacy)

**Type:** Exec (In → Out)  
**Category:** PLAYER

> **Deprecated.** For new projects use **Hit Scan Weapon**, which supports multiple origins, pellets, tracers, and hybrid modes.

Fires a raycast from the camera or the object when `weapon_fired = True` and publishes hit data. Applies damage to the hit object through the `globalDict` channel.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Damage | Float (≥0) | `25.0` | Damage per successful hit |
| Range | Float (≥0.1) | `100.0` | Maximum raycast distance (Range units) |
| Property | String | `""` | BGE filter: only hits objects with this property (empty = any) |
| Camera Origin | Bool | `True` | `True` = raycast from active camera (FPS); `False` = from object |
| Ignore Self | Bool | `True` | Skips the owner object when raycasting (Camera Origin = False only) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published variables

| Variable | Type | Description |
|----------|------|-------------|
| `weapon_hit` | bool | `True` on the hit frame |
| `weapon_hit_object` | KX_GameObject | Hit object (or `None`) |
| `weapon_hit_position` | Vector | Hit position in world space |
| `weapon_hit_normal` | Vector | Normal of the hit surface |

## Damage

Damage is written to `Range.logic.globalDict['_dmg_<object_name>']`. The target object must read and consume this key in its own logic to apply the damage.

## Spread

If **Weapon Spread** is in the graph before this node, it reads `weapon_spread_pitch` and `weapon_spread_yaw` (in radians) and deflects the raycast accordingly.

## Graph position

```
[Weapon Fire] → [Weapon Hitscan (Legacy)] → [Weapon FX (Legacy)]
```

## Migration to the new system

| Legacy | New system |
|--------|------------|
| Weapon Hitscan (Legacy) | Hit Scan Weapon |
| `weapon_hit_*` | Same name in Hit Scan Weapon |
| No pellets | Hit Scan Weapon with `Pellets > 1` |
| No tracer | Hit Scan Weapon with `Tracer FX` |
