# Vehicle Autonomous

**Tipo:** Acción  
**Categoría:** VEHICLE

Vehículo controlado por inteligencia artificial. Puede seguir un objetivo o patrullar entre waypoints, con evasión de obstáculos opcional y reset automático de vuelco.

## Propiedades principales

### Ruedas y suspensión

Mismos parámetros que [Vehicle Setup](vehicle-setup.md): Front Left/Right, Rear Left/Right, Wheel Radius, Stiffness, Damping, Compression, Tyre Friction, Roll Influence.

### Comportamiento IA

| Propiedad | Por defecto | Descripción |
|-----------|-------------|-------------|
| Mode | `Follow Target` | `Follow Target` (sigue un objeto) o `Patrol` (recorre waypoints) |
| Target | — | Objeto a seguir (solo `Follow Target`) |
| Waypoints | `2` | Número de waypoints (2–6, solo `Patrol`) |
| WP 1–6 | — | Objetos waypoint en orden de patrulla |

### Motor IA

| Propiedad | Por defecto | Descripción |
|-----------|-------------|-------------|
| Drive Type | `RWD` | Tracción |
| Max Engine Force | `600.0` | Fuerza máxima de motor |
| Max Brake Force | `20.0` | Fuerza máxima de freno |
| Max Steer (rad) | `0.45` | Ángulo máximo de dirección |
| Max Speed (km/h) | `60.0` | Velocidad máxima |
| Steer Sensitivity | `2.5` | Factor de corrección de dirección |
| Stop Distance | `3.0` | Distancia a la que empieza a frenar |
| Arrive Radius | `4.0` | Radio para considerar un waypoint alcanzado |
| Brake Anticipation | `0.6` | Reduce motor en curvas para evitar derrapes |

### Extras

| Propiedad | Por defecto | Descripción |
|-----------|-------------|-------------|
| Obstacle Avoidance | `True` | Raycasts laterales para esquivar obstáculos |
| Auto-Reset on Flip | `True` | Endereza el vehículo si se vuelca |
| Store Speed in Property | `False` | Guarda velocidad en `self.own['speed_kmh']` |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |
| Arrived | Salida | Exec |

## Uso típico

### Vehículo que persigue al jugador

```
[On Update] → [Vehicle Autonomous:
                   Mode=Follow Target, Target=player
                   Max Speed=60  Stop Distance=4.0
                   Obstacle Avoidance=True]
                  └── Out     ──► [...]
                  └── Arrived ──► [...]  # cuando llega al objetivo
```

### Taxi en patrulla

```
[On Update] → [Vehicle Autonomous:
                   Mode=Patrol
                   WP 1=stop_a  WP 2=stop_b  WP 3=stop_c
                   Max Speed=40  Arrive Radius=3.0]
```

## Notas

- El objeto debe tener física **Vehicle** en Range Game Engine.
- La rama `Arrived` se activa cada frame mientras el vehículo está dentro de `Stop Distance` del objetivo.
- `Obstacle Avoidance` usa raycasts laterales de 7 unidades — ajusta `Stop Distance` para evitar falsos positivos.
