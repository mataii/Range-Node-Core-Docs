# PHYSICS Category

Nodes for controlling object physics at runtime: forces, velocities, gravity, mass, and physics suspension.

## Available Nodes

| Node | Type | Description |
|------|------|-------------|
| [Apply Force](apply-force.md) | Action | Applies a force to the object |
| [Apply Torque](apply-torque.md) | Action | Applies torque (rotation force) to the object |
| [Set Linear Velocity](set-linear-velocity.md) | Action | Sets linear velocity directly |
| [Set Angular Velocity](set-angular-velocity.md) | Action | Sets angular velocity directly |
| [Get Velocity](get-velocity.md) | Data | Returns linear velocity components |
| [Set Gravity](set-gravity.md) | Action | Changes the global scene gravity |
| [Set Damping](set-damping.md) | Action | Changes linear and angular damping |
| [Set Mass](set-mass.md) | Action | Changes the object's mass |
| [Suspend Physics](suspend-physics.md) | Action | Deactivates object physics |
| [Restore Physics](restore-physics.md) | Action | Reactivates object physics |

## Notes

- Force and velocity nodes only work on objects with active physics (not static).
- `Set Gravity` affects all scene objects that have gravity enabled.
