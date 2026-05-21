# Replace Mesh

**Type:** Action  
**Category:** OBJECT

Replaces the object's mesh with the mesh from another scene object using `replaceMesh()`.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Mesh Object | String | `"mesh_source"` | Name of the object whose mesh will be used as source |
| Use Display | Bool | `True` | Replaces the display mesh |
| Use Physics | Bool | `True` | Also replaces the physics mesh |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
self.own.replaceMesh('mesh_source', True, True)
```

## Typical usage

### Change weapon model on equip

```
[On Message: Subject="equip_rifle"]
    → [Replace Mesh: Mesh Object="mesh_rifle"]
```

### Progressive damage visual

```
[On Update] → [If Property: prop="health", Op=<, Value=50]
                  └── True ──► [Replace Mesh: Mesh Object="damaged_hull"]
```

### Manual LOD

```
[On Update] → [If Property: prop="dist_to_cam", Op=>, Value=20]
                  ├── True ──► [Replace Mesh: Mesh Object="low_poly_version"]
                  └── False ──► [Replace Mesh: Mesh Object="high_poly_version"]
```

## Notes

- The source object (`Mesh Object`) must exist in the scene — can be in an inactive layer.
- If `Use Physics=True`, physics is recalculated for the new mesh — may be expensive for complex meshes.
- The material does not change — only the geometry.
