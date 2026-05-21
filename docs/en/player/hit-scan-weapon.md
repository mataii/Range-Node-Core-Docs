# Hit Scan Weapon

**Type:** Exec (In → Out)  
**Category:** PLAYER

Modern hitscan weapon system. Fires one or more instant raycasts when `weapon_fired = True`, applies damage to hit objects via `globalDict`, and publishes hit data for FX and downstream logic.

Supports multiple pellets (shotgun), BGE property filter, team filter, and optional tracers.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Damage | Float | `25.0` | Damage per raycast/pellet that hits |
| Range | Float | `100.0` | Maximum raycast distance (Range units) |
| Pellets | Int (1–32) | `1` | Rays per shot (`1` = pistol/rifle, `>1` = shotgun) |
| Property | String | `""` | BGE filter — only hits objects with this property (empty = any) |
| Origin | Enum | `Camera` | Ray origin |
| Ignore Owner | Bool | `True` | Excludes the owner object from raycasting (Object/Hybrid modes) |
| Team Filter | Bool | `False` | Ignores hits on objects from the same team |
| Team Property | String | `"team"` | BGE property used to identify teams |
| Tracer FX | Bool | `False` | Spawns a tracer object at the shot origin |
| Tracer Object | String | `"TracerFX"` | Name of the inactive tracer object |
| Tracer Frames | Int | `6` | Tracer lifetime in frames |

### Origin modes

| Mode | Description |
|------|-------------|
| `Camera` | Ray from the active camera (standard FPS) |
| `Object` | Ray from this object (AI / third person) |
| `Hybrid` | Object position + camera direction (converges at crosshair) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published variables

| Variable | Type | Description |
|----------|------|-------------|
| `weapon_hit` | bool | `True` if at least one pellet hit |
| `weapon_hit_object` | KX_GameObject | Last object hit |
| `weapon_hit_position` | mathutils.Vector | Position of the last hit |
| `weapon_hit_normal` | mathutils.Vector | Normal of the hit surface |

## Damage

Damage is applied via `globalDict` with the key `_dmg_<object_name>`. The damaged object must have a node that reads this key (e.g. **Enemy Perception**, **Player Health** with a Damage socket connected to a node that reads from globalDict).

## Weapon Spread integration

Reads `weapon_spread_pitch` and `weapon_spread_yaw` to deflect the ray direction if Weapon Spread is present.

## Graph position

```
[Weapon Spread] → [Hit Scan Weapon] → [Weapon FX System]
```
