# Enemy Rotation

**Type:** Exec (In → Out)  
**Category:** AI  
**Execution Order:** 56 (after Enemy Movement)

Rotates the owner object to face `ai_target_pos`. Supports smoothed (slerp) or instant rotation.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Rotation Speed | Float (≥0.1) | `8.0` | Angular rotation speed |
| Smooth | Bool | `True` | Interpolate rotation (True) or instant (False) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Variables read

| Variable | Source |
|----------|--------|
| `ai_target_pos` | Enemy Perception |

## Behavior

Each frame:

1. Reads `ai_target_pos`.
2. If it's a valid Vector: computes XY direction to the target.
3. Computes the required rotation angle (`atan2` of the cross product with the object's Y forward vector).
4. Applies rotation:
   - **Smooth = True:** scales angle by `min(1.0, rotation_speed * deltaTime * 60)` — framerate-independent progressive rotation.
   - **Smooth = False:** applies the full angle instantly.
5. If `ai_target_pos` is None (target not visible): no rotation.

## Typical usage

### Enemy that always faces the player when visible

```
[Enemy Perception] → [Enemy Movement] → [Enemy Rotation]
```

Rotation only occurs when `ai_target_pos` is valid (target visible). When the player is out of sight, the enemy keeps facing its last known direction.

### Tuning rotation speed

```
Rotation Speed: 4.0   # slow turn, tank
Rotation Speed: 15.0  # fast turn, drone
```

The scale factor is `rotation_speed * deltaTime * 60`, so behavior is framerate-consistent.

## Notes

- The rotation axis is always Z (horizontal rotation). The node doesn't handle vertical tilting.
- The forward axis of the object is `-Y` (`to_track_quat('-Y', 'Z')`). If your model faces a different direction, rotate the mesh inside the object or add a rotation offset node.
- When `ai_target_pos` is None (target out of sight or dead), Enemy Rotation does nothing — the object stays at its last known orientation.
- To rotate toward the movement destination instead of the target, use a BTCustomTask with the same logic but reading `ai_move_target`.
