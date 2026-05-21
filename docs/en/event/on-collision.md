# On Collision

**Type:** Branch (Hit / None)  
**Category:** EVENT

Detects physical collisions with the object that owns the component. Branches execution based on whether a collision occurred in the current frame.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Prop Filter | String | `""` | If non-empty, only detects collisions with objects that have a BGE property with this name |
| Consume | Bool | `True` | If True, clears the collision flag after reading it (single-fire per collision) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Hit | Output | Exec (collision detected) |
| None | Output | Exec (no collision) |
| Object | Output | Data (String — name of the colliding object) |

## Mechanism

On Collision uses `collisionCallbacks`, registered once via a `hasattr` guard:

```python
# Registered once in start() or on first update():
def _rnc_col_cb_<name>(obj, point, normal):
    self.own['_rnc_col_<name>'] = obj.name

if not hasattr(self, '_rnc_col_<name>_reg'):
    self.own.collisionCallbacks.append(_rnc_col_cb_<name>)
    self._rnc_col_<name>_reg = True
```

The collision flag is stored as a BGE property on the object itself:

```python
_obj = self.own.get('_rnc_col_<name>', None)
if _obj is not None:
    # Hit path — _obj contains the colliding object's name
    if True:  # consume=True
        del self.own['_rnc_col_<name>']
else:
    # None path
```

## Consume behavior

| consume | Behavior |
|---------|----------|
| `True` | The flag is cleared after reading — fires once per collision event |
| `False` | The flag persists — fires every frame while the collision remains active |

**Note with consume=False:** if multiple objects collide in the same frame, only the last one's name is stored (each callback overwrites the previous value).

## Prop Filter

When `Prop Filter` is set to a non-empty string, only collisions with objects that have a BGE property of that name are detected. Objects without that property are ignored even if they physically collide.

```python
# With Prop Filter = "enemy":
if obj.get('enemy', None) is not None:
    self.own['_rnc_col_<name>'] = obj.name
```

## Typical usage

### Detect any collision

```
[On Update] → [On Collision]
                  └── Hit ──► [BTCustomTask: print("hit by", _obj)]
```

### Filter by object type

```
[On Update] → [On Collision: Prop Filter="bullet"]
                  └── Hit ──► [Damage Receiver logic]
```

### Check what was hit (Object socket)

```
[On Update] → [On Collision]
                  └── Hit ──► [BTCustomTask:
                                   if _obj == 'Player':
                                       self._bt_bb['scored'] = True]
```

### Persistent collision (consume=False)

```
[On Update] → [On Collision: consume=False]
                  ├── Hit  ──► [BTCustomTask: self.own['touching'] = True]
                  └── None ──► [BTCustomTask: self.own['touching'] = False]
```

## Notes

- The callback is registered only once — calling the node in update() does not accumulate multiple callbacks.
- The colliding object's name is stored, not a reference — use `Range.logic.getCurrentScene().objects.get(_obj)` to retrieve the object from the name.
- For objects with `No Collision` physics type, the callback never fires.
- On Collision does not distinguish between the object's mesh faces — it detects any contact with the bounding volume.
- If the object is destroyed and recreated, the callback must be re-registered (On Start handles this automatically on the new instance).
