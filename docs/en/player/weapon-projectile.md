# Weapon Projectile

**Type:** Exec (In → Out)  
**Category:** PLAYER

Spawns and configures a projectile object in the scene when `weapon_fired = True`. Applies direction, speed, and spread offset to the newly instantiated projectile. Collision, damage, and lifetime logic belongs to the **Projectile System** node placed on the projectile object.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Object Name | String | `"Projectile"` | Name of the inactive scene object |
| Speed | Float | `50.0` | Initial projectile speed (units/s) |
| Lifetime | Int | `180` | Projectile lifetime in frames |
| Spawn Offset | Float | `1.0` | Forward distance from origin before positioning the projectile |
| Active Duration | Float | `0.1` | Seconds `weapon_projectile_active` stays `True` after spawn |
| Camera Direction | Bool | `True` | Uses active camera direction (FPS). Disable for object origin (AI) |
| Inherit Velocity | Bool | `False` | Adds the object's linear velocity to the projectile |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published variables

| Variable | Type | Description |
|----------|------|-------------|
| `weapon_projectile_spawned` | bool | One-frame pulse when the projectile spawns |
| `weapon_projectile_active` | bool | `True` for `Active Duration` seconds after spawn |

## Weapon Spread integration

Reads `weapon_spread_pitch` and `weapon_spread_yaw` to deflect the launch direction if Weapon Spread is present.

## Projectile object requirements

- Must exist as an **inactive** object in the scene.
- Must have active physics (Dynamic or Character) for collision detection.
- Flight and collision behavior is configured with **Projectile System** on the projectile object itself.

## Graph position

```
[Weapon Spread] → [Weapon Projectile] → [Weapon FX System]
```

The projectile object has its own RNC graph with:
```
[OnUpdate] → [Projectile System]
```
