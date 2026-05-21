# Categoría PARTICLE

Nodos para controlar sistemas de partículas en tiempo de ejecución. Permiten iniciar, detener y gestionar emisores de partículas de la escena.

## Nodos disponibles

| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Start Particles](start-particles.md) | Acción | Activa un emisor de partículas |
| [Stop Particles](stop-particles.md) | Acción | Desactiva un emisor de partículas |
| [Spawn Particles](spawn-particles.md) | Acción | Instancia un emisor de partículas desde la biblioteca |
| [Set Particle Visible](set-particle-visible.md) | Acción | Muestra, oculta o alterna la visibilidad de un emisor |

## Notas

- `Start Particles` combina `setVisible(True)` con `reinstancePhysicsMesh()` para reiniciar la simulación.
- `Spawn Particles` usa `scene.addObject()` — el emisor debe existir en una capa inactiva o en la biblioteca de la escena.
- Para efectos de una sola vez (explosiones, impactos), `Spawn Particles` con un `lifetime` corto es el patrón recomendado.
