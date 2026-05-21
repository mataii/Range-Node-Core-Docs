# Vehicle Setup

**Type:** Action  
**Category:** VEHICLE

Initializes the Range Game Engine vehicle constraint and registers the four wheels with their suspension and friction parameters. Runs only on the first frame (`hasattr` guard).

## Properties

| Property | Default | Description |
|----------|---------|-------------|
| Front Left | — | Front-left wheel object |
| Front Right | — | Front-right wheel object |
| Rear Left | — | Rear-left wheel object |
| Rear Right | — | Rear-right wheel object |
| Wheel Radius | `0.35` | Wheel radius in units |
| Susp. Length | `0.4` | Suspension travel length |
| Stiffness | `50.0` | Suspension stiffness |
| Damping | `10.0` | Suspension damping |
| Compression | `4.0` | Suspension compression |
| Tyre Friction | `4.0` | Tyre friction coefficient |
| Roll Influence | `0.05` | Roll influence (0 = no rollover, 1 = maximum) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
if not hasattr(self, '_veh_ready'):
    _veh_scene = Range.logic.getCurrentScene()
    self._vehicle = Range.constraints.createVehicle(self.own.getPhysicsId())
    if 'wheel_fl' in _veh_scene.objects:
        _w = _veh_scene.objects['wheel_fl']
        _side = -1.0 if _w.localPosition.x <= 0 else 1.0
        self._vehicle.addWheel(_w, _w.localPosition,
            mathutils.Vector((0, 0, -1)),
            mathutils.Vector((_side, 0, 0)),
            0.4, 0.35, True)
    # ... (remaining wheels)
    for _wi in range(self._vehicle.getNumWheels()):
        self._vehicle.setTyreFriction(_wi, 4.0)
        self._vehicle.setSuspensionStiffness(_wi, 50.0)
        self._vehicle.setSuspensionDamping(_wi, 10.0)
        self._vehicle.setSuspensionCompression(_wi, 4.0)
        self._vehicle.setRollInfluence(_wi, 0.05)
    self._veh_ready = True
```

## Typical usage

```
[On Update]
    → [Vehicle Setup: FL=wheel_fl, FR=wheel_fr, RL=wheel_rl, RR=wheel_rr
           Wheel Radius=0.35, Tyre Friction=4.5, Stiffness=60]
    → [Vehicle Throttle: ...]
    → [Vehicle Steering: ...]
```

## Notes

- The component's object must have **Vehicle** physics type in Range Game Engine.
- Wheels are registered based on local position — those with `localPosition.x ≤ 0` are treated as left side.
- Low `Roll Influence` (0.05) prevents easy flipping; increasing it gives more realistic physics.
