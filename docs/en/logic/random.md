# Random

**Type:** Data  
**Category:** LOGIC

Returns a random float in the range [0.0, 1.0). Legacy node — for more complete options use the MATH category nodes (RandomFloat, RandomInt, etc.).

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Value | Output | Data (Float) |

## Generated code

```python
__import__('random').random()
```

## Typical usage

### Condition on probability

```
[On Update] → [BT Condition: {Random} < 0.3]
                  └── True ──► [...]  # occurs 30% of frames
```

## Notes

- Returns a different value every frame — do not use for cross-frame comparisons without storing the result.
- For a custom range, use [Random Float](../math/random-float.md). For seeding, use [Set Random Seed](../math/set-random-seed.md).
- Marked as legacy because [Random Float](../math/random-float.md) provides the same functionality with more options.
