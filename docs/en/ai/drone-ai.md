# Drone AI

**Type:** Action  
**Category:** AI

Controls an autonomous 3D flying drone. Implements weighted Seek + Obstacle Avoidance behaviour, maintaining a configurable hover height and horizontal follow distance relative to a target. Place on the drone object and connect to `On Update`.

## Properties

### Target

| Property | Default | Description |
|----------|---------|-------------|
| Target | — | Object to follow in the scene |

### Movement

| Property | Default | Description |
|----------|---------|-------------|
| Speed | `4.0` | Maximum flight speed |
| Vel Lerp | `0.08` | Velocity smoothing (1 = instant, lower = more inertia) |
| Rot Lerp | `0.05` | Rotation smoothing toward the target |

### Positioning

| Property | Default | Description |
|----------|---------|-------------|
| Hover Height | `3.0` | Height above the target |
| Follow Distance | `5.0` | Horizontal distance maintained |
| Height Deadzone | `0.3` | Minimum vertical difference before correcting height |

### Obstacle Avoidance

| Property | Default | Description |
|----------|---------|-------------|
| Probes | `6 — Full` | Number of avoidance raycasts: `6 — Full` or `2 — Minimal` (perf) |
| Avoid Radius | `2.0` | Obstacle detection range |
| Avoid Weight | `1.5` | Weight of the avoidance direction vs. seek direction |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code (simplified)

```python
import mathutils as _mu
_dt = Range.logic.deltaTime()
if not hasattr(self, '_drone_rdy'):
    self._drone_rdy = True
    self._drone_vel = _mu.Vector((0.0, 0.0, 0.0))
    self._drone_probes = (
        _mu.Vector(( 1, 0, 0)), _mu.Vector((-1, 0, 0)),
        _mu.Vector(( 0, 1, 0)), _mu.Vector(( 0,-1, 0)),
        _mu.Vector(( 0, 0, 1)), _mu.Vector(( 0, 0,-1)),
    )
_drone_target = self.own.scene.objects.get('player')
# ... Seek toward target + height
# ... Avoidance raycasts in 6 directions
# _drone_steer = seek_dir + avoid_dir * avoid_weight
self._drone_vel = self._drone_vel.lerp(_drone_steer * 4.0, 0.08)
self.own.applyMovement(self._drone_vel * _dt, False)
# Smooth Yaw rotation toward target
```

## Typical usage

### Surveillance drone

```
[On Update] → [Drone AI:
                   Target=player
                   Speed=5.0
                   Hover Height=4.0
                   Follow Distance=6.0
                   Avoid Radius=2.5]
```

### Low-performance drone (dense object areas)

```
[On Update] → [Drone AI:
                   Target=player
                   Speed=3.0
                   Probes=2 — Minimal]
```

## Notes

- The object should have **No Collision** or **Ghost** physics to move freely in 3D.
- `applyMovement` in world space (`False`) — the drone does not inherit parent object rotation.
- `Probes=Minimal` uses only 2 raycasts (forward + down) for better performance.
- Multiple drones each have their own `_drone_*` state thanks to the `hasattr` guard.
