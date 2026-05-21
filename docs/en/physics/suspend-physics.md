# Suspend Physics

**Type:** Action  
**Category:** PHYSICS

Suspends the object's physics simulation. The object stops responding to forces, gravity, and collisions.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Ghost | Bool | `False` | If `True`, object becomes a ghost (detectable but no solid collision) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
self.own.suspendPhysics(False)
```

## Typical usage

### Freeze object when game pauses

```
[On Message: Subject="pause"]
    → [Suspend Physics]
```

### Suspend as ghost (detectable but no collision)

```
[On Message: Subject="go_ghost"]
    → [Suspend Physics: Ghost=True]
```

### Suspend on death (animate death without physics)

```
[On Message: Subject="die"]
    → [Suspend Physics]
    → [Play Action: Action="death_anim"]
```

## Notes

- Use [Restore Physics](restore-physics.md) to reactivate simulation.
- With `Ghost=True`, the object still reports overlaps but exerts no physical reaction.
- The object's position is not updated by physics while suspended, but can still be moved via Transform or scripts.
