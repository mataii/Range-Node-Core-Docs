# FPS

Nodos especializados para juegos en primera y tercera persona en Range Game Engine. Incluyen movimiento, cámara, raycast de disparo y herramientas de alineación.

## Nodos

| Nodo | Tipo | Descripción |
|------|------|-------------|
| [FPS Raycast](fps-raycast.md) | Acción (rama) | Raycast desde la cámara o el objeto con salidas directas de objeto/posición/normal |
| [Mouse Look](mouse-look.md) | Acción | Control de cámara con el ratón (Yaw + Pitch) |
| [Spawn At Hit](spawn-at-hit.md) | Acción | Spawnea un objeto en el punto de impacto del último raycast |
| [Align To Normal](align-to-normal.md) | Acción | Alinea el objeto a la normal de la superficie del último raycast |
| [FPS Tools](fps-tools.md) | Acción | Controlador completo FPS/TPS: movimiento, salto, sprint, mouselook e inventario |

## Notas

- `FPS Tools` es un prefab todo en uno — útil para prototipos rápidos.
- Para proyectos avanzados, usa `Mouse Look` + `FPS Raycast` + lógica de movimiento propia.
- `FPS Raycast` difiere de `Raycast` en la categoría RAYCAST: tiene salidas directas de objeto/posición/normal y puede emitir desde la cámara automáticamente.
