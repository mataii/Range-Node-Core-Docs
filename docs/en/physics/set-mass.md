# Set Mass

**Type:** Action  
**Category:** PHYSICS

Changes the object's mass at runtime, affecting how it responds to forces and impulses.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Mass | Float | `1.0` | New mass of the object in kg |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
self.own.mass = 1.0
```

## Typical usage

### Object that absorbs water and becomes heavier

```
[On Collision: Object="water"]
    → [Set Mass: Mass=10.0]
```

### Reset mass on water exit

```
[On End Collision: Object="water"]
    → [Set Mass: Mass=1.0]
```

### Immovable object (huge mass to resist collisions)

```
[On Start] → [Set Mass: Mass=999.0]
```

## Notes

- A mass of `0.0` makes the object static (does not respond to forces).
- Greater mass requires more force to achieve the same acceleration (F = ma).
- Does not affect the object's visual size or scale.
