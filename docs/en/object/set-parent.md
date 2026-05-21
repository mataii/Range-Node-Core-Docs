# Set Parent

**Type:** Action  
**Category:** OBJECT

Sets the parent of the current object in the scene hierarchy.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Parent | String | `"parent_obj"` | Name of the new parent object |
| Compound | Bool | `False` | If True, combines physics with the parent |
| Ghost | Bool | `False` | If True, the child is a ghost (no own collisions) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
self.own.setParent(scene.objects['parent_obj'], False, False)
```

## Typical usage

### Pick up object (attach to player's hand)

```
[On Collision: Object="Player"] → [Set Parent: Parent="hand_R"]
```

### Attach shield to character

```
[On Message: Subject="equip_shield"] → [Set Parent: Parent="arm_L", Compound=True]
```

## Notes

- `Compound=True` merges the child's physics with the parent's — useful for equippable objects.
- `Ghost=True` makes the child non-collidable while parented.
- To detach, use [Remove Parent](remove-parent.md).
