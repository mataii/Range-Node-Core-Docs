# Camera Follow

**Tipo:** Acción  
**Categoría:** CAMERA

Mueve la cámara suavemente hacia un objeto objetivo usando interpolación lineal (lerp) para la posición y, opcionalmente, slerp para la orientación.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Target | String | `"Player"` | Nombre del objeto a seguir |
| Offset | Vector | `[0, -5, 2]` | Desplazamiento desde el objetivo en espacio de mundo |
| Position Speed | Float | `0.1` | Factor de lerp para posición [0.0–1.0] |
| Rotation Speed | Float | `0.0` | Factor de slerp para rotación. 0.0 = sin rotación automática |
| Camera | String | `""` | Cámara a mover. Vacío = `scene.active_camera` |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado (simplificado)

```python
_cf_target = scene.objects['Player']
_cf_target_pos = _cf_target.worldPosition + Vector([0, -5, 2])
_cf_cam = scene.active_camera
_cf_cam.worldPosition = _cf_cam.worldPosition.lerp(_cf_target_pos, 0.1)
# Si rotation_speed > 0:
_cf_cam.worldOrientation = _cf_cam.worldOrientation.slerp(_cf_target.worldOrientation, 0.0)
```

## Uso típico

### Cámara de seguimiento de tercera persona

```
[On Update] → [Camera Follow:
                   Target = "Player"
                   Offset = [0, -6, 3]
                   Position Speed = 0.08]
```

### Cámara cinematográfica que sigue y mira al personaje

```
[On Update] → [Camera Follow:
                   Target = "Player"
                   Offset = [3, -4, 2]
                   Position Speed = 0.05
                   Rotation Speed = 0.05]
```

## Notas

- El `Offset` se aplica en espacio de mundo — no rota con el objetivo. Para offset relativo al frente del objetivo, usa `BTCustomTask` con `worldOrientation * Vector(offset)`.
- Con `Position Speed=1.0`, la cámara se teletransporta instantáneamente al objetivo.
- Para tercera persona con rotación de cámara controlada por el jugador, usa [FPS Tools](../fps/fps-tools.md) o el sistema [Mouse Look](../fps/mouse-look.md).
