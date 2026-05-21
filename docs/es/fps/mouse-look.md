# Mouse Look

**Tipo:** Acción  
**Categoría:** FPS

Controla la cámara con el ratón: rota el cuerpo del personaje en Yaw (horizontal) y la cámara en Pitch (vertical). Incluye suavizado y clampeo de pitch.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Body | Object | — | Objeto que rota en Yaw (horizontal). Vacío = usa el objeto del componente |
| Camera | Object | — | Objeto que rota en Pitch (vertical). Normalmente la cámara |
| Sensitivity | Float | `2.0` | Velocidad de rotación con el ratón |
| Mouse Smooth | Float | `0.7` | Suavizado: 1 = instantáneo, menor = más suave |
| Clamp Pitch (rad) | Float | `1.4` | Límite de rotación vertical en radianes (~80°) |
| Invert Y | Bool | `False` | Invierte el eje vertical del ratón |
| Yaw (Z) | Bool | `True` | Habilita la rotación horizontal |
| Pitch (X) | Bool | `True` | Habilita la rotación vertical |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
if not hasattr(self, '_ml_ready'):
    self._ml_pitch = 0.0
    self._ml_sx    = 0.0
    self._ml_sy    = 0.0
    Range.logic.mouse.visible  = False
    Range.logic.mouse.position = (0.5, 0.5)
    self._ml_ready = True
_ml_body = self.own.scene.objects.get('player_body') or self.own
_ml_cam  = self.own.scene.objects.get('camera')
_ml_ms   = Range.logic.mouse
_ml_rx   = (_ml_ms.position[0] - 0.5) * 2.0
_ml_ry   = (_ml_ms.position[1] - 0.5) * 2.0
_ml_ms.position = (0.5, 0.5)
self._ml_sx = self._ml_sx * 0.3 + _ml_rx * 0.7
self._ml_sy = self._ml_sy * 0.3 + _ml_ry * 0.7
_ml_body.applyRotation((0.0, 0.0, -self._ml_sx), False)
if _ml_cam:
    _ml_delta = -self._ml_sy
    if abs(self._ml_pitch + _ml_delta) <= 1.4:
        self._ml_pitch += _ml_delta
        _ml_cam.applyRotation((_ml_delta, 0.0, 0.0), True)
```

## Uso típico

### Controlador FPS manual

```
[On Start] → [Capture Mouse]

[On Update] → [Mouse Look: Body=player_body, Camera=fps_camera, Sensitivity=2.5]
           → [...]  # movimiento WASD, salto, etc.
```

## Notas

- El ratón se oculta y se centra automáticamente en el primer frame.
- Almacena `self._fps_cam` si se usa junto con `FPS Tools` (lo lee `FPS Raycast`).
- Para control de cámara en tercera persona, usa [FPS Tools](fps-tools.md) con `Perspective=Third Person`.
