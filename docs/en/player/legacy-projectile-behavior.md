# Projectile Behavior (Legacy)

**Type:** Exec (In → Out)  
**Category:** PLAYER

> **Deprecated.** For new projects use **Projectile System** (together with Weapon Projectile for spawning), which separates the spawn and behavior responsibilities and supports trail FX and better pool integration.

Node that attaches to the **projectile object** (not the player). Manages its lifecycle: lifetime, collisions, damage, and destruction. Supports gravity and object pool return.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Damage | Float (≥0) | `25.0` | Damage applied to the hit object |
| Lifetime | Float (≥0.1) | `3.0` | Seconds until auto-destruction |
| Destroy on Hit | Bool | `True` | Destroys the projectile on first hit |
| Property Filter | String | `""` | Only collides with objects that have this BGE property (empty = any) |
| Gravity | Bool | `False` | Applies downward gravity each frame |
| Gravity Strength | Float (≥0) | `9.8` | Downward acceleration (units/s²) |
| Use Object Pool | Bool | `False` | Returns projectile to pool instead of calling `endObject()` |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published variables

| Variable | Type | Description |
|----------|------|-------------|
| `projectile_hit` | bool | One-frame pulse on collision |
| `projectile_hit_object` | KX_GameObject | Hit object (or `None`) |
| `projectile_hit_position` | Vector | Hit position |
| `projectile_hit_normal` | Vector | Normal of the hit surface |

## Collision detection

The node uses a predictive raycast based on the object's linear velocity (`worldLinearVelocity`). Each frame it fires a ray of length `speed * deltaTime + 0.1` units in the movement direction. This detects collisions even for fast projectiles that might pass through thin geometry in a single frame.

## Damage

Damage is written to `Range.logic.globalDict['_dmg_<object_name>']`. The target object must read and consume this key in its own logic.

## Gravity

When `Gravity = True`, each frame subtracts `gravity_strength * deltaTime` from the Z component of `worldLinearVelocity`. It does not use the engine's physics system for gravity — it's manual accumulation for greater control.

## Object Pool integration

When `Use Object Pool = True`, on death the node:

1. Sets `_pool_active = False` on the object.
2. Stops linear velocity.
3. Moves the object to the parking position (`_pool_parking_x/y/z`).
4. Calls `suspendDynamics(True)` if the method exists.

If the object does not have `_pool_active`, falls back to `endObject()`.

## Graph position

This node attaches to the **projectile object**, not the player:

```
[OnUpdate] → [Projectile Behavior (Legacy)] → [Out]
```

The projectile is spawned by the player's firing code (or a WeaponFire-type node on the owner object).

## Migration to the new system

| Legacy | New system |
|--------|------------|
| Projectile Behavior (Legacy) | Weapon Projectile (spawn) + Projectile System (behavior) |
| Manual gravity | Projectile System with `Use Gravity` |
| Use Object Pool | Projectile System with `Use Pool` |
| projectile_hit_* | Same name in Projectile System |
| No trail | Projectile System with `Trail FX` |
