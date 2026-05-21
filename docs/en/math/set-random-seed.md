# Set Random Seed

**Type:** Action  
**Category:** MATH

Sets the Python random number generator seed to obtain reproducible sequences.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Seed | Int | `42` | Generator seed. Must be an integer |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |
| Seed | Input | Data (Int) |

## Generated code

```python
__import__('random').seed(42)
```

## Typical usage

### Reproducible procedural generation

```
[On Start] → [Set Random Seed: Seed={Get Property: prop="world_seed"}]
           → [BTCustomTask:
                  # generate map with random.randint / random.choice...
                  # always produces the same result with the same seed]
```

### Debug: reproduce a specific scenario

```
[On Start] → [Set Random Seed: Seed=12345]
           → [...]
```

## Notes

- Affects Python's `random` module globally — all random nodes that use `random.*` will be affected.
- Calling `Set Random Seed` mid-sequence resets the generator, which can break reproducibility if there are other random calls in between.
- For time-based seeds (non-reproducible behavior), use `[BTCustomTask: import random; random.seed(int(Range.logic.getClockTime() * 1000))]`.
