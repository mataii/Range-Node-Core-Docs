# Flee

**Type:** Exec (In → Out)  
**Category:** AI

Moves the owner object away from a target until the safe radius is reached. When the target is farther than `Safe Radius`, the object gradually decelerates.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Target | Enum | *(scene objects)* | Object to flee from |
| Speed | Float (≥0.01) | `4.0` | Flee speed (units/s) |
| Safe Radius | Float (≥0.1) | `8.0` | Distance at which the enemy feels "safe" |
| Vel Lerp | Float (0.01–1.0) | `0.2` | Velocity smoothing (1=instant) |
| Physics | Enum | `Character` | Object physics mode |

### Physics modes

| Mode | Movement method |
|------|-----------------|
| `Character` | `applyMovement()` in world space |
| `Dynamic` | Assigns `localLinearVelocity.x/y` (preserves Z) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Behavior

Each frame:

1. Computes `direction = own_position - target_position` (away direction).
2. Ignores the Z axis (horizontal movement only).
3. If `distance < safe_radius`: applies velocity in the away direction.
4. If `distance ≥ safe_radius`: gradually decelerates (lerp toward zero).

Velocity is stored in `self._fl_vel` and interpolated with `Vel Lerp` each frame.

## Typical usage

### Basic flee from player

```
Target: Player
Speed: 5.0
Safe Radius: 10.0
Vel Lerp: 0.1
```

```
[OnUpdate] → [Flee: Player]
```

### Conditional flee with Distance Check

```
[Distance Check: Player, Threshold=10.0]
    ├── Near ──► [Flee: Player]     # flee if player is close
    └── Far  ──► [Patrol]            # patrol if player is far
```

## Notes

- If the Target doesn't exist in the scene, the node doesn't move and produces no error.
- `self._fl_vel` is shared across all Flee nodes in the same component — if there are two Flee nodes in the same tree, they share the internal velocity. Rename nodes to differentiate them.
- Useful combined with **AI State Machine**: when the state is RETREAT, connect to Flee instead of Enemy Movement.
