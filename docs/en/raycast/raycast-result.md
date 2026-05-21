# Raycast Result

**Type:** Data  
**Category:** RAYCAST

Reads the result stored by a [Raycast](raycast.md), [General Raycast](general-raycast.md), or [Mouse Pick](mouse-pick.md) node with the same Ray ID. Both nodes must be on the same object.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Ray ID | String | `"ray1"` | Must match the raycast node's Ray ID |
| Result | Enum | `Hit Name` | Which value to return (see table) |

### Result options

| Value | Return type | Description |
|-------|-------------|-------------|
| `Hit Name` | String | Name of the hit object (empty string on miss) |
| `Hit (bool)` | Bool | True if something was hit |
| `Hit Pos X/Y/Z` | Float | X, Y, or Z coordinate of the hit point |
| `Normal X/Y/Z` | Float | X, Y, or Z component of the surface normal |
| `Distance` | Float | Distance from ray origin to hit point |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Value | Output | Data (type depends on Result) |

## Generated code

```python
# Result = Hit Name
(getattr(self, '_rc_ray1_hit', None).name if getattr(self, '_rc_ray1_hit', None) else '')

# Result = Hit (bool)
(getattr(self, '_rc_ray1_hit', None) is not None)

# Result = Hit Pos X
(getattr(self, '_rc_ray1_pos', [0.0, 0.0, 0.0])[0])

# Result = Distance
((lambda _hp: ((_hp[0]-self.own.worldPosition.x)**2 + ...)**0.5
  if getattr(self, '_rc_ray1_hit', None) else 0.0)
 (getattr(self, '_rc_ray1_pos', [0.0, 0.0, 0.0])))
```

## Typical usage

### Display name of aimed object

```
[On Update]
    → [Raycast: Direction=Forward, Distance=5.0, Ray ID="aim"]
          └── Hit  ──► [Set Text: Text={Raycast Result: Ray ID="aim", Result=Hit Name}]
          └── Miss ──► [Set Text: Text=""]
```

### Spawn particles at hit point

```
[On Key Press: Key=SPACE, Mode=PRESSED]
    → [Raycast: Direction=Forward, Distance=20.0, Ray ID="shot"]
          └── Hit  ──► [Add Object:
                           Object="spark_particle"
                           X={Raycast Result: Ray ID="shot", Result=Hit Pos X}
                           Y={Raycast Result: Ray ID="shot", Result=Hit Pos Y}
                           Z={Raycast Result: Ray ID="shot", Result=Hit Pos Z}]
          └── Miss ──► [...]
```

## Notes

- This node does NOT perform any raycast — it only reads what the executor node stored.
- The raycast must run in the same frame for the result to be current.
- If the raycast has never run, `Hit (bool)` returns `False` and positions return `[0,0,0]`.
