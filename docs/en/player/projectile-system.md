# Projectile System

**Type:** Exec (In → Out)  
**Category:** PLAYER

Complete in-flight projectile behavior. Placed on the **projectile object** (not the player). Detects collisions, applies damage, manages lifetime, gravity, object pool, and trail effects.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Damage | Float | `25.0` | Damage delivered to the hit object |
| Lifetime | Float | `3.0` | Seconds until auto-destruction |
| Destroy on Hit | Bool | `True` | Destroys/releases the projectile on first valid hit |
| Property Filter | String | `""` | Only collides with objects that have this BGE property |
| Gravity | Bool | `False` | Applies downward gravity each frame |
| Gravity Strength | Float | `9.8` | Fall acceleration (units/s²) |
| Use Object Pool | Bool | `False` | Returns projectile to pool on death instead of destroying it |
| Ignore Owner | Bool | `True` | Ignores collisions with the object that spawned it (reads `_proj_owner`) |
| Team Filter | Bool | `False` | Ignores collisions with objects from the same team |
| Team Property | String | `"team"` | BGE property used to identify teams |
| Trail FX | Bool | `False` | Spawns and parents a trail object on birth |
| Trail Object | String | `"ProjectileTrail"` | Name of the inactive trail object |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published variables (on the projectile object)

| Variable | Type | Description |
|----------|------|-------------|
| `projectile_hit` | bool | `True` on the hit frame |
| `projectile_hit_object` | KX_GameObject | Hit object |
| `projectile_hit_position` | mathutils.Vector | Hit position |
| `projectile_hit_normal` | mathutils.Vector | Surface normal |
| `projectile_damage` | float | Configured damage (modifiable at runtime) |

## Damage

Damage is applied through `globalDict['_dmg_<object_name>']`, the same as **Hit Scan Weapon**.

## Object Pool integration

When `Use Object Pool = True`, on death the projectile calls the pool system instead of `endObject()`. The pool must be configured on the **player** object (not on the projectile).

## Projectile object graph

```
[OnUpdate] → [Projectile System]
                 │
                 └── (Out) → [Explosion Damage] (if explosive)
                           → [Weapon FX System] (if has own FX)
```
