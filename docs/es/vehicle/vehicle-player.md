# Vehicle Player

**Tipo:** Acción  
**Categoría:** VEHICLE

Controlador todo en uno para vehículos controlados por el jugador. Incluye inicialización de ruedas, motor, frenos, dirección, cámara de seguimiento opcional, turbo y reset automático de vuelco.

## Propiedades principales

### Ruedas y suspensión

| Propiedad | Por defecto | Descripción |
|-----------|-------------|-------------|
| Front Left / Right / Rear Left / Right | — | Objetos de las cuatro ruedas |
| Wheel Radius | `0.35` | Radio de rueda |
| Stiffness / Damping / Compression | `50/10/4` | Parámetros de suspensión |
| Tyre Friction | `4.0` | Fricción del neumático |
| Roll Influence | `0.05` | Influencia del roll |

### Motor

| Propiedad | Por defecto | Descripción |
|-----------|-------------|-------------|
| Drive Type | `RWD` | Tracción: AWD / RWD / FWD |
| Max Engine Force | `800.0` | Fuerza máxima de motor |
| Max Brake Force | `25.0` | Fuerza máxima de freno |
| Max Steer (rad) | `0.5` | Ángulo máximo de dirección |
| Max Speed (km/h) | `120.0` | Velocidad máxima |
| Steer Return | `0.08` | Velocidad de retorno del volante al centro |

### Teclas

W/S (aceleración/reversa), A/D (dirección), Space (freno), Shift (turbo opcional), Ctrl (marcha atrás)

### Extras

| Propiedad | Por defecto | Descripción |
|-----------|-------------|-------------|
| Turbo | `False` | Multiplicador de fuerza con tecla de turbo |
| Auto-Reset on Flip | `True` | Endereza el vehículo si se vuelca |
| Store Speed in Property | `False` | Guarda velocidad en `self.own['speed_kmh']` |

### Cámara (opcional)

| Propiedad | Por defecto | Descripción |
|-----------|-------------|-------------|
| Camera | — | Objeto cámara de la escena |
| Cam Distance | `6.0` | Distancia de la cámara al chasis |
| Cam Height | `2.5` | Altura de la cámara sobre el chasis |
| Cam Smoothing | `0.1` | Suavizado de posición de cámara (lerp) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Uso típico

```
[On Update] → [Vehicle Player:
                   Front Left=wheel_fl  Front Right=wheel_fr
                   Rear Left=wheel_rl   Rear Right=wheel_rr
                   Drive=RWD
                   Max Engine=800  Max Speed=120
                   Camera=follow_cam
                   Auto-Reset=True]
```

## Notas

- Conectar a `On Update` — gestiona toda la lógica de vehículo cada frame.
- El objeto debe tener física **Vehicle** en Range Game Engine.
- Para control más granular, usa [Vehicle Setup](vehicle-setup.md) + [Vehicle Throttle](vehicle-throttle.md) + [Vehicle Steering](vehicle-steering.md) por separado.
