# Restore Physics

**Type:** Action  
**Category:** PHYSICS

Reactivates the object's physics simulation after it has been suspended with [Suspend Physics](suspend-physics.md).

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
self.own.restorePhysics()
```

## Typical usage

### Resume physics when game unpauses

```
[On Message: Subject="unpause"]
    → [Restore Physics]
```

### Activate physics after an animation sequence

```
[On Message: Subject="spawn_done"]
    → [Restore Physics]
```

### Suspend / restore cycle

```
[On Key Press: Key=P, Mode=PRESSED]
    → [Toggle: prop="physics_paused"]

[On Update] → [If Property: prop="physics_paused", Value=True]
                  └── True  ──► [Suspend Physics]
                  └── False ──► [Restore Physics]
```

## Notes

- Has no effect if physics was not suspended.
- The object resumes with the velocity and state it had before suspension.
- Natural pair of [Suspend Physics](suspend-physics.md).
