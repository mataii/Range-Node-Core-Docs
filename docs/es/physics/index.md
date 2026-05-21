# Categoría PHYSICS

Nodos para controlar la física de objetos en tiempo de ejecución: fuerzas, velocidades, gravedad, masa y suspensión de física.

## Nodos disponibles

| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Apply Force](apply-force.md) | Acción | Aplica una fuerza al objeto |
| [Apply Torque](apply-torque.md) | Acción | Aplica un torque (rotación) al objeto |
| [Set Linear Velocity](set-linear-velocity.md) | Acción | Establece la velocidad lineal directamente |
| [Set Angular Velocity](set-angular-velocity.md) | Acción | Establece la velocidad angular directamente |
| [Get Velocity](get-velocity.md) | Dato | Devuelve componentes de la velocidad lineal |
| [Set Gravity](set-gravity.md) | Acción | Cambia la gravedad global de la escena |
| [Set Damping](set-damping.md) | Acción | Cambia el amortiguamiento lineal y angular |
| [Set Mass](set-mass.md) | Acción | Cambia la masa del objeto |
| [Suspend Physics](suspend-physics.md) | Acción | Desactiva la física del objeto |
| [Restore Physics](restore-physics.md) | Acción | Reactiva la física del objeto |

## Notas

- Los nodos de fuerza y velocidad solo funcionan en objetos con física activa (no estáticos).
- `Set Gravity` afecta a todos los objetos de la escena que tengan gravedad habilitada.
