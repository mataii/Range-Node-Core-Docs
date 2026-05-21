# FPS Tools

**Tipo:** Acción  
**Categoría:** FPS

Controlador todo en uno para juegos en primera o tercera persona. Gestiona movimiento WASD, salto, sprint, agacharse, mouselook y variables de inventario opcionales. Ideal para prototipos rápidos.

## Propiedades

### Cámara / Vista

| Propiedad | Por defecto | Descripción |
|-----------|-------------|-------------|
| Camera | — | Objeto de cámara de la escena |
| Perspective | `First Person` | `First Person` o `Third Person` |
| Distance | `4.0` | Distancia de la cámara al personaje (solo TPS) |
| Sensitivity | `2.0` | Sensibilidad del ratón |
| Mouse Smooth | `0.7` | Suavizado del ratón (1 = instantáneo) |
| Clamp Pitch | `1.4` | Límite de pitch vertical en radianes |
| Invert Y | `False` | Invertir eje vertical del ratón |

### Movimiento

| Propiedad | Por defecto | Descripción |
|-----------|-------------|-------------|
| Physics | `Character` | `Character` (usa `walkDirection`) o `Dynamic` (usa `applyMovement`) |
| Walk Speed | `5.0` | Velocidad al caminar |
| Sprint Speed | `10.0` | Velocidad al correr |
| Jump Force | `7.0` | Impulso de salto |
| Acceleration | `0.25` | Lerp de aceleración/desaceleración |
| Air Control | `0.3` | Control horizontal en el aire (0–1) |

### Teclas

| Propiedad | Por defecto |
|-----------|-------------|
| Forward | W |
| Backward | S |
| Left | A |
| Right | D |
| Jump | Space |
| Sprint | Left Shift |
| Crouch | Left Ctrl |
| Interact | E |

### Variables de inventario (opcionales)

| Propiedad | Por defecto | Descripción |
|-----------|-------------|-------------|
| Health | `True`, Max=100 | Crea propiedad BGE `health` |
| Armor | `False`, Max=100 | Crea propiedad BGE `armor` |
| Ammo | `True`, Max=30 | Crea propiedad BGE `ammo` |
| Reserve | `True`, Max=90 | Crea propiedad BGE `ammo_reserve` |
| Score | `False` | Crea propiedad BGE `score` |
| Stamina | `False`, Max=100 | Crea propiedad BGE `stamina`, limita sprint |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Uso típico

### Controlador FPS básico

```
[On Update] → [FPS Tools:
                   Camera=fps_camera
                   Perspective=First Person
                   Walk Speed=5.0
                   Sprint Speed=10.0
                   Jump Force=7.0
                   Health=True, Max HP=100
                   Ammo=True, Max Ammo=30]
```

## Propiedades BGE creadas al iniciar

```
self.own['health']      = 100.0  # si Health=True
self.own['ammo']        = 30     # si Ammo=True
self.own['ammo_reserve']= 90     # si Reserve=True
self.own['is_crouching']= False
self.own['is_sprinting']= False
self.own['is_grounded'] = False
```

## Notas

- Conectar a `On Update` — gestiona mouselook y movimiento cada frame.
- El ratón se oculta y centra automáticamente en el primer frame.
- Para mecánicas adicionales (disparo, interacción), usa [FPS Raycast](fps-raycast.md) después de este nodo.
- Para control granular, usa [Mouse Look](mouse-look.md) y lógica de movimiento propia en lugar de este nodo.
