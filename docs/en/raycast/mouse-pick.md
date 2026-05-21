# Mouse Pick

**Type:** Action (branch)  
**Category:** RAYCAST

Casts a ray from the active camera through the mouse cursor position. Ideal for 3D object selection, point-and-click targeting, and interaction systems.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Ray ID | String | `"mpick"` | Shared identifier with Raycast Result |
| Distance | Float | `100.0` | Maximum ray length |
| Prop Filter | String | `""` | Only hit objects with this BGE property |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Hit | Output | Exec |
| Miss | Output | Exec |

## Generated code

```python
_mp_cam  = self.own.scene.active_camera
_mp_mpos = Range.logic.mouse.position
_mp_vec  = _mp_cam.getScreenVect(_mp_mpos[0], _mp_mpos[1])
_mp_orig = _mp_cam.worldPosition.copy()
_mp_to   = _mp_orig - _mp_vec * 100.0
_mp_res  = _mp_cam.rayCast(_mp_to, _mp_orig, 100.0, '', 0, 0, 0)
self._rc_mpick_hit = _mp_res[0]
self._rc_mpick_pos = list(_mp_res[1]) if _mp_res[1] else [0.0, 0.0, 0.0]
self._rc_mpick_nrm = list(_mp_res[2]) if _mp_res[2] else [0.0, 0.0, 1.0]
if _mp_res[0] is not None:
    # Hit branch
else:
    # Miss branch
```

## Typical usage

### Highlight object under cursor

```
[On Update]
    → [Mouse Pick: Distance=50.0, Ray ID="hover"]
          └── Hit  ──► [BTCustomTask:
                            _hit = getattr(self, '_rc_hover_hit', None)
                            if _hit: _hit['highlighted'] = True]
          └── Miss ──► [...]
```

### Place object at click point

```
[On Mouse Button: Button=LEFT, Mode=PRESSED]
    → [Mouse Pick: Distance=200.0, Prop Filter="terrain", Ray ID="place"]
          └── Hit  ──► [Add Object:
                           Object="building"
                           X={Raycast Result: Ray ID="place", Result=Hit Pos X}
                           Y={Raycast Result: Ray ID="place", Result=Hit Pos Y}
                           Z={Raycast Result: Ray ID="place", Result=Hit Pos Z}]
          └── Miss ──► [...]
```

## Notes

- The ray is cast from `scene.active_camera`, not from the object that holds the component.
- Results stored in `self._rc_<ID>_hit/pos/nrm`, same as all other raycast nodes.
- Use [Raycast Result](raycast-result.md) with the same `Ray ID` to read name, position and normal.
- Combine with `Prop Filter` to detect only specific object types (terrain, enemies, etc.).
