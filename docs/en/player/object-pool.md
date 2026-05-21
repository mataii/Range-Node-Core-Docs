# Object Pool

**Type:** Exec (In → Out)  
**Category:** PLAYER

Pre-instantiates a fixed number of objects at game start and parks them off-screen. Consumer nodes (such as Weapon Projectile or Projectile System) acquire and release objects from the pool without calling `addObject()`/`endObject()` every frame, reducing garbage collection overhead.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Object Name | String | `""` | Name of the inactive template object in the scene |
| Pool Size | Int (1–256) | `10` | Objects to pre-instantiate at startup |
| Park X | Float | `-9999.0` | X position where inactive objects are parked (off-screen) |
| Park Spacing | Float | `2.0` | Y spacing between parked objects |
| Dynamic Expand | Bool | `False` | Spawns an extra object when pool is exhausted (maximum 2× Pool Size) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published variables

| Variable | Type | Description |
|----------|------|-------------|
| `_pool_objects` | list | List of references to the pool objects |
| `pool_available` | int | Currently free objects available |
| `pool_active_count` | int | Objects currently in use |

## How it works

1. On the first frame, the node calls `addObject()` for each pool slot and parks objects at `(Park X, i * Park Spacing, 0)`.
2. To acquire an object from the pool, a consumer finds the first one with `_pool_active = False`, activates the flag, and repositions it.
3. To release an object, the consumer (Projectile System with `Use Pool = True`) deactivates `_pool_active` and returns the object to its parking position.

## Graph position

Must be **before** any node that consumes the pool in the same frame:

```
[OnUpdate] → [Object Pool] → [Weapon Projectile: Use Pool] → ...
```

## When to use a pool

| Scenario | Recommended size |
|----------|-----------------|
| Pistol (slow fire) | 5–10 |
| Automatic rifle | 15–20 |
| Shotgun (multi-pellet) | 20–30 |
| Multiple enemies firing | 30–50+ |
