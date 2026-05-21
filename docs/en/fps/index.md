# FPS

Specialized nodes for first-person and third-person games in Range Game Engine. Includes movement, camera, shooting raycast, and alignment tools.

## Nodes

| Node | Type | Description |
|------|------|-------------|
| [FPS Raycast](fps-raycast.md) | Action (branch) | Raycast from camera or object with direct object/position/normal outputs |
| [Mouse Look](mouse-look.md) | Action | Mouse camera control (Yaw + Pitch) |
| [Spawn At Hit](spawn-at-hit.md) | Action | Spawn an object at the last raycast hit point |
| [Align To Normal](align-to-normal.md) | Action | Align the object to the surface normal of the last raycast hit |
| [FPS Tools](fps-tools.md) | Action | All-in-one FPS/TPS controller: movement, jump, sprint, mouselook, and inventory |

## Notes

- `FPS Tools` is an all-in-one prefab — ideal for rapid prototyping.
- For advanced projects, combine `Mouse Look` + `FPS Raycast` with custom movement logic.
- `FPS Raycast` differs from `Raycast` in the RAYCAST category: it has direct object/position/normal output sockets and can cast from the camera automatically.
