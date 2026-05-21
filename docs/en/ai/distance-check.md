# Distance Check

**Type:** Branch (Near / Far)  
**Category:** AI

Measures the distance between the owner object and a scene target, then branches execution. Also exposes the computed distance in the local variable `_ai_dist` for downstream nodes.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Target | Enum | *(scene objects)* | Reference object |
| Threshold | Float (≥0.01) | `5.0` | Distance limit to separate Near/Far |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Near | Output | Exec (distance ≤ threshold) |
| Far | Output | Exec (distance > threshold) |
| Distance | Output | Data (float) |

## Behavior

Each frame:

1. Looks up the Target object in the scene.
2. If found: computes 3D distance to `worldPosition`.
3. If not found: distance = 9999.
4. If `distance ≤ threshold` → executes `Near`.
5. If `distance > threshold` → executes `Far`.

The local variable `_ai_dist` remains available in the generated code for downstream nodes.

## Typical usage

### Behavior branching by distance

```
[Distance Check: Player, Threshold=10.0]
    ├── Near ──► [Seek: Player]
    └── Far  ──► [Patrol]
```

### Attack zone

```
[Distance Check: Player, Threshold=2.0]
    ├── Near ──► [BT Custom Task: attack]
    └── Far  ──► [Seek: Player]
```

### Chained with Line Of Sight

```
[Distance Check: Player, Threshold=15.0]
    ├── Near ──► [Line Of Sight: Player]
    │               ├── Visible ──► [Seek: Player]
    │               └── Hidden  ──► [Wander]
    └── Far  ──► [Patrol]
```

## Notes

- Distance is calculated in 3D (includes height difference). For XY-only distance, use a BTCustomTask.
- The `Distance` socket is a Data type — connect it to another data node that reads it. It is not published to the scene.
- If the Target is not in the scene, the node always chooses `Far` (distance = 9999).
- Distance Check does not update any `ai_*` variables. For the modular system, use **Enemy Perception** which publishes `ai_dist`.
