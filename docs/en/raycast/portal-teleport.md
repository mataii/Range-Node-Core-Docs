# Portal Teleport

**Type:** Action  
**Category:** RAYCAST

Teleports the object between two portals in the style of Valve's Portal game: preserves relative velocity and reorients the object according to the exit portal's transform, including the standard 180° flip.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Destination | Enum | `Target Object` | `XYZ Coordinates` (manual) or `Target Object` (using portal objects) |
| Portal In (A) | Object | — | Object representing the entry portal |
| Portal Out (B) | Object | — | Object representing the exit portal |
| X / Y / Z | Float | `0.0` | Manual destination (only with `XYZ Coordinates`) |
| Launch on Exit | Bool | `True` | Redirects linear velocity to the new reference frame |
| Exit Offset | Float | `1.0` | Offset from exit portal to avoid embedding in geometry |
| Preserve Angular Velocity | Bool | `False` | Also redirects angular velocity |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| X / Y / Z | Input | Data (Float) — manual destination, overrides property |
| Out | Output | Exec |

## Generated code (Target Object mode)

```python
# -- Portal Teleport --
import mathutils as _mu
_pt_scene = self.own.scene
_pt_b = _pt_scene.objects.get('portal_b')
if _pt_b:
    _pt_b_mat = _pt_b.worldTransform.copy()
    _pt_b_ori = _pt_b.worldOrientation.copy()
    _pt_a = _pt_scene.objects.get('portal_a')
    if _pt_a:
        _pt_a_ori = _pt_a.worldOrientation.copy()
        _pt_rel_ori = _pt_a_ori.inverted() * self.own.worldOrientation
        _pt_flip = _mu.Matrix.Rotation(3.14159265, 3, 'Z')
        _pt_new_ori = _pt_b_ori * _pt_flip * _pt_rel_ori
        self.own.worldOrientation = _pt_new_ori
        _pt_forward = _pt_b_ori * _mu.Vector((0.0, -1.0, 0.0))
        _pt_new_pos = _pt_b.worldPosition + _pt_forward * 1.0
        self.own.worldPosition = _pt_new_pos
        # Redirect linear velocity
        _pt_vel = self.own.linearVelocity.copy()
        if _pt_vel.length > 0.001:
            _pt_vel_local = _pt_a_ori.inverted() * _pt_vel
            _pt_vel_new   = _pt_b_ori * (_pt_flip * _pt_vel_local)
            self.own.linearVelocity = _pt_vel_new
        else:
            self.own.linearVelocity = _mu.Vector((0, 0, 0))
```

## Typical usage

### Classic portal between two marker objects

```
[On Collision: Object="portal_trigger_a"]
    → [Portal Teleport:
           Destination=Target Object
           Portal In=portal_a
           Portal Out=portal_b
           Launch on Exit=True
           Exit Offset=1.2]
```

### Simple teleport to coordinates

```
[On Key Press: Key=T, Mode=PRESSED]
    → [Portal Teleport:
           Destination=XYZ Coordinates
           X=10.0  Y=0.0  Z=1.0
           Portal Out=checkpoint_marker
           Launch on Exit=False]
```

## Notes

- `Portal Out` determines the arrival orientation and is required.
- `Portal In` is optional — without it, relative orientation is not transformed.
- `Exit Offset` prevents the object from getting stuck inside portal geometry on arrival.
- Place this node on the travelling object, not on the portals themselves.
