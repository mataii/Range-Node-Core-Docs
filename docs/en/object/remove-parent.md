# Remove Parent

**Type:** Action  
**Category:** OBJECT

Removes the parent of the current object, leaving it as a root-level object in the scene.

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
self.own.removeParent()
```

## Typical usage

### Drop a held object

```
[On Key Press: Key=E, Mode=PRESSED] → [Remove Parent]
```

### Detach on death

```
[On Message: Subject="player_dead"] → [Remove Parent]
```

## Notes

- If the object has no parent, `removeParent()` has no effect.
- When the parent is removed, the object retains its current world position and becomes independent with its own physics.
