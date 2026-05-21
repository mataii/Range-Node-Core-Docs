# Wander

**Type:** Exec (In → Out)  
**Category:** AI

Moves the object in a random direction that changes periodically. Produces organic, meandering movement — ideal for idle roaming without a fixed destination.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Speed | Float (≥0.01) | `2.0` | Wander speed (units/s) |
| Min Interval (s) | Float (≥0.1) | `1.0` | Minimum seconds before changing direction |
| Max Interval (s) | Float (≥0.1) | `3.0` | Maximum seconds before changing direction |
| Dir Lerp | Float (0.005–1.0) | `0.05` | Turn smoothing (lower = wider curves) |
| Physics | Enum | `Character` | Object physics mode |

### Physics modes

| Mode | Method |
|------|--------|
| `Character` | `applyMovement()` × `deltaTime()` |
| `Dynamic` | Assigns `localLinearVelocity.x/y` |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Behavior

Each frame:

1. Decrements the timer `self._wd_timer`.
2. If the timer reaches 0: picks a random angle between -π and π, computes a new target direction `self._wd_tgt`, and resets the timer to a random value between Min and Max Interval.
3. Interpolates `self._wd_dir` toward `self._wd_tgt` with `Dir Lerp` each frame.
4. Applies movement in the interpolated direction.

The `random` and `math` modules are imported only when the direction changes (on average every 1–3 s), not every frame.

## Effect of Dir Lerp

| Dir Lerp | Behavior |
|----------|----------|
| `0.005` | Very slow, wide turns — nearly straight trajectory |
| `0.05` | Moderate turns — natural roaming (recommended) |
| `0.3` | Fast turns — erratic movement |
| `1.0` | Instant turn — abrupt direction changes |

## Typical usage

### Basic wander

```
Speed: 2.0
Min Interval: 1.5
Max Interval: 4.0
Dir Lerp: 0.05
Physics: Character
```

```
[OnUpdate] → [Wander]
```

### Wander + Distance Check (approach if player spotted)

```
[Distance Check: Player, Threshold=12.0]
    ├── Near ──► [Seek: Player]
    └── Far  ──► [Wander]
```

## Notes

- The Z axis is always 0 in movement — the object doesn't fly.
- The first frame starts with `_wd_dir = (0, 1, 0)` (positive Y direction). The first direction change occurs after `random.uniform(min, max)` seconds.
- There is no built-in obstacle detection. If the object collides with geometry, it will keep pushing in the same direction until the timer changes.
- To add obstacle avoidance, combine Wander with a manual raycast in BTCustomTask, or use Enemy AI which includes obstacle avoidance.
