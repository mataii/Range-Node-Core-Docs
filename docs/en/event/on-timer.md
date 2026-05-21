# On Timer

**Type:** Branch (Elapsed / Waiting)  
**Category:** EVENT

Internal countdown timer. Fires the `Elapsed` path when the countdown reaches zero, then optionally resets.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Duration | Float | `2.0` | Countdown time in seconds |
| Repeat | Bool | `True` | If True, resets automatically after firing |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Elapsed | Output | Exec (timer reached zero) |
| Waiting | Output | Exec (still counting down) |
| Progress | Output | Data (Float 0.0 → 1.0) |

## Generated code

```python
# Initialization (first call):
if not hasattr(self, '_tmr_<name>'):
    self._tmr_<name> = 2.0  # duration

# Each frame:
if self._tmr_<name> >= 0:
    self._tmr_<name> -= Range.logic.deltaTime()

if self._tmr_<name> <= 0 and self._tmr_<name> > -1.0:
    _tmr_progress = 1.0
    if True:  # repeat=True
        self._tmr_<name> = 2.0  # reset
    else:
        self._tmr_<name> = -1.0  # permanent silence
    # Elapsed path
else:
    _tmr_progress = max(0.0, 1.0 - self._tmr_<name> / 2.0)
    # Waiting path
```

## Progress socket

`Progress` goes from `0.0` (timer just reset) to `1.0` (timer elapsed). Use it for smooth UI fills or animation blends:

| Time elapsed | Progress value |
|--------------|---------------|
| 0 s | 0.0 |
| 1 s (of 2 s) | 0.5 |
| 2 s | 1.0 |

## Repeat behavior

| Repeat | After Elapsed fires |
|--------|---------------------|
| `True` | Resets to duration — fires again after another full interval |
| `False` | Sets timer to `-1.0` — **both** Elapsed and Waiting stop executing permanently |

When `Repeat=False`, On Timer fires `Elapsed` exactly once, then silences itself completely. Neither path executes again until the component is recreated.

## Typical usage

### Periodic event

```
[On Update] → [On Timer: Duration=5.0, Repeat=True]
                  └── Elapsed ──► [BTCustomTask: spawn_wave()]
```

### One-shot trigger

```
[On Update] → [On Timer: Duration=3.0, Repeat=False]
                  └── Elapsed ──► [BTCustomTask: end_intro()]
```

### Loading bar

```
[On Update] → [On Timer: Duration=10.0, Repeat=False]
                  ├── Waiting ──► [BTCustomTask: hud['bar'] = _tmr_progress]
                  └── Elapsed ──► [BTCustomTask: start_game()]
```

### Cooldown

```
[On Update] → [On Timer: Duration=0.5, Repeat=True]
                  └── Elapsed ──► [Enemy Combat]
```

## On Timer vs Delay (FLOW)

| On Timer | Delay (FLOW) |
|----------|-------------|
| Own entry point not required — has In socket | Requires On Update as root |
| Countdown with `deltaTime()` | Absolute clock `getClockTime()` |
| Exposes Progress socket (0 → 1) | No progress |
| Repeat = True by default | Repeat = False by default |
| Affected by slow framerate | Not affected (absolute time) |

Use **On Timer** when you need progress feedback or simpler setup inside an existing chain. Use **Delay** when you need precision independent of frame rate fluctuation.

## Notes

- `_tmr_progress` is available as a local variable in nodes connected to both Elapsed and Waiting paths.
- On Timer counts down using `deltaTime()` — if the game runs slowly, ticks are larger and the timer is still accurate in real time.
- Multiple On Timer nodes in the same tree each maintain their own `_tmr_<name>` variable (namespaced by node name).
- Resetting the timer programmatically: `del self._tmr_<name>` in a [BTCustomTask](../ai/bt-custom-task.md) — the next frame re-initializes it to the full duration.
