# On Mouse Move

**Tipo:** Branch (Moving / Still)  
**Categoría:** EVENT

Detecta movimiento del cursor del ratón comparando su posición entre frames. Expone los deltas de movimiento en X e Y como sockets de datos.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Threshold | Float (0.0001–0.1) | `0.001` | Movimiento mínimo para considerar que el ratón se está moviendo |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Moving | Salida | Exec (movimiento > threshold) |
| Still | Salida | Exec (sin movimiento) |
| Delta X | Salida | Data (Float) |
| Delta Y | Salida | Data (Float) |

## Comportamiento

```python
if not hasattr(self, '_mm_last'):
    self._mm_last = Range.logic.mouse.position

_mm_cur = Range.logic.mouse.position
_mm_dx  = _mm_cur[0] - self._mm_last[0]
_mm_dy  = _mm_cur[1] - self._mm_last[1]
self._mm_last = _mm_cur

if abs(_mm_dx) > threshold or abs(_mm_dy) > threshold:
    # → Moving
else:
    # → Still
```

### Sistema de coordenadas

`Range.logic.mouse.position` devuelve valores normalizados en el rango `[0, 1]`:
- `(0, 0)` = esquina superior izquierda
- `(1, 1)` = esquina inferior derecha

Los deltas también están normalizados. Para convertir a píxeles, multiplicar por la resolución de pantalla.

## Variables de datos expuestas

Los sockets `Delta X` y `Delta Y` exponen las variables locales `_mm_dx` y `_mm_dy`. Se pueden leer en nodos de dato conectados:

```python
# En un BTCustomTask conectado después:
dx = _mm_dx   # disponible como variable local
dy = _mm_dy
```

## Uso típico

### Rotar cámara con el ratón

```
[On Update] → [On Mouse Move: Threshold=0.001]
                  └── Moving ──► [BTCustomTask]
```
```python
# BTCustomTask:
cam = self.own.scene.active_camera
cam.applyRotation((0, 0, -_mm_dx * 0.5), False)
cam.applyRotation((_mm_dy * 0.5, 0, 0), True)
```

### Detectar inactividad del ratón

```
[On Update] → [On Mouse Move]
                  ├── Moving ──► [BT Set Blackboard: mouse_idle_timer = 0]
                  └── Still  ──► [BTCustomTask:
                                    self._bt_bb['mouse_idle_timer'] = self._bt_bb.get('mouse_idle_timer', 0) + Range.logic.deltaTime()]
```

### Activar cursor visual al mover

```
[On Update] → [On Mouse Move]
                  ├── Moving ──► [BTCustomTask: show_cursor()]
                  └── Still  ──► (cursor ya visible, no hacer nada)
```

## Ajuste del Threshold

| Threshold | Sensibilidad |
|-----------|-------------|
| `0.0001` | Muy sensible — detecta movimientos mínimos (tembladera) |
| `0.001` | Normal — ignora micro-vibraciones del sensor |
| `0.01` | Poco sensible — solo movimientos amplios |

## Notas

- El primer frame siempre va a `Still` (la posición inicial se inicializa igual a la actual → delta = 0).
- Si el cursor está bloqueado en el centro de la pantalla (modo FPS), los deltas seguirán funcionando porque `mouse.position` reporta la posición antes del recentrado.
- Los deltas son siempre en espacio de pantalla normalizado — no dependen de la resolución.
- Para scroll del ratón (rueda), usar un nodo de la categoría **MOUSE** o leer `Range.logic.mouse.wheel`.
