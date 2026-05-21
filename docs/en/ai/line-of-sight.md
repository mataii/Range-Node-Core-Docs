# Line Of Sight

**Type:** Branch (Visible / Hidden)  
**Category:** AI

Checks whether the owner object has a direct line of sight to a target using a raycast. Branches execution based on the result.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Target | Enum | *(scene objects)* | Reference object |
| Max Distance | Float (≥0.1) | `15.0` | Maximum detection distance |
| Obstacle Filter | String | `""` | BGE property that blocks the raycast (empty = any object) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Visible | Output | Exec (target in sight) |
| Hidden | Output | Exec (target hidden or out of range) |

## Behavior

Each frame:

1. Looks up the Target in the scene.
2. If not found: executes `Hidden`.
3. Computes distance to the target.
4. Casts a ray from the owner to the target's position:
   ```python
   hit, _, _ = self.own.rayCast(
       target.worldPosition, self.own.worldPosition,
       max_distance, prop_filter, 1, 1
   )
   ```
5. Visible if: `hit == target` and `distance ≤ max_distance`.

### Obstacle Filter

If `Obstacle Filter` is empty, **any object** between the owner and the target blocks line of sight. If set (e.g. `"wall"`), only objects with that BGE property block it. Use this so water, triggers, or transparent zones don't interrupt detection.

## Typical usage

### Simple detection

```
[Line Of Sight: Player, Max Distance=15.0]
    ├── Visible ──► [Seek: Player]
    └── Hidden  ──► [Wander]
```

### Distance Check + Line Of Sight (optimization)

Raycasts are expensive — limit distance first to save calls:

```
[Distance Check: Player, Threshold=15.0]
    ├── Near ──► [Line Of Sight: Player, Max Distance=15.0]
    │               ├── Visible ──► [Seek: Player]
    │               └── Hidden  ──► [Patrol]
    └── Far  ──► [Patrol]
```

## Difference from Enemy Perception

| Line Of Sight | Enemy Perception |
|---------------|-----------------|
| One raycast per frame | Configurable: every N seconds |
| No FOV | Configurable angular FOV |
| No memory | Last-seen position memory |
| Doesn't publish `ai_*` | Publishes full `ai_*` contract |
| Standalone | Part of the modular system |

## Notes

- The raycast is cast from the owner object's origin, not from its "eyes". For tall enemies, the ray may pass under obstacles. Use a child Empty at eye height and BTCustomTask for precise line-of-sight.
- The node uses `rayCast(target, own, distance, prop, 1, 1)` with xray=1 — the ray penetrates the owner object and won't block itself.
- If the target is exactly at `max_distance`, it is considered `Hidden`.
