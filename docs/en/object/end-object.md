# End Object

**Type:** Action  
**Category:** OBJECT

Destroys the object running the component at the end of the current frame using `self.own.endObject()`.

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |

## Generated code

```python
self.own.endObject()
```

## Typical usage

### Destroy projectile on impact

```
[On Collision: Property="solid"] → [End Object]
```

### Destroy enemy on death

```
[On Message: Subject="enemy_dead"] → [End Object]
```

### Destroy pickup when collected

```
[On Collision: Object="Player"] → [End Object]
```

## Notes

- `endObject()` schedules destruction at the end of the frame — the object still exists during the current frame. Do not continue executing object-dependent logic after calling `endObject()`.
- Has no `Out` socket because subsequent code is potentially unsafe (the object is marked for destruction).
- To destroy a different object, use `BTCustomTask: scene.objects['name'].endObject()`.
