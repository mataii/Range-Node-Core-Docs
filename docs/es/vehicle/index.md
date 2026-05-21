# VEHICLE

Nodos para crear y controlar vehículos con física de ruedas (Vehicle Constraint) en Range Game Engine. Incluyen controladores completos para jugador e IA.

## Nodos

| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Vehicle Setup](vehicle-setup.md) | Acción | Inicializa el constraint de vehículo y configura las ruedas |
| [Vehicle Throttle](vehicle-throttle.md) | Acción | Aplica fuerza de motor (AWD / RWD / FWD) |
| [Vehicle Brake](vehicle-brake.md) | Acción | Aplica fuerza de freno a las ruedas |
| [Vehicle Steering](vehicle-steering.md) | Acción | Establece el ángulo de dirección en las ruedas delanteras |
| [Vehicle Speed](vehicle-speed.md) | Dato | Devuelve la velocidad actual en m/s y km/h |
| [Vehicle Reset](vehicle-reset.md) | Acción | Endereza el vehículo si está volcado |
| [Vehicle Player](vehicle-player.md) | Acción | Controlador completo de vehículo para el jugador con cámara opcional |
| [Vehicle Autonomous](vehicle-autonomous.md) | Acción | Vehículo controlado por IA: perseguir objetivo o patrullar waypoints |

## Flujo típico (manual)

```
[On Update] → [Vehicle Setup: FL=wheel_fl, FR=wheel_fr, RL=wheel_rl, RR=wheel_rr]

[On Key Press: Key=W, Mode=HELD]
    → [Vehicle Throttle: Force=800, Drive=RWD]

[On Key Press: Key=A, Mode=HELD]
    → [Vehicle Steering: Angle=0.5]

[On Key Press: Key=SPACE, Mode=HELD]
    → [Vehicle Brake: Force=25, Wheels=All]
```

## Notas

- `Vehicle Setup` solo inicializa una vez (usa `hasattr` guard) — puede estar en `On Update`.
- Para prototipos rápidos, usa `Vehicle Player` que incluye toda la lógica en un solo nodo.
- Las ruedas deben ser objetos hijos del chasis en la escena.
