# Drone AI

**Tipo:** Acción  
**Categoría:** AI

Controla un dron volador autónomo en 3D. Implementa un comportamiento de Seek + Evasión de Obstáculos ponderada, manteniendo una altura y distancia horizontal configurables respecto al objetivo. Coloca en el objeto dron y conecta a `On Update`.

## Propiedades

### Target

| Propiedad | Por defecto | Descripción |
|-----------|-------------|-------------|
| Target | — | Objeto a seguir en la escena |

### Movement

| Propiedad | Por defecto | Descripción |
|-----------|-------------|-------------|
| Speed | `4.0` | Velocidad máxima de vuelo |
| Vel Lerp | `0.08` | Suavizado de velocidad (1 = instantáneo, menor = más inercia) |
| Rot Lerp | `0.05` | Suavizado de rotación hacia el objetivo |

### Positioning

| Propiedad | Por defecto | Descripción |
|-----------|-------------|-------------|
| Hover Height | `3.0` | Altura sobre el objetivo |
| Follow Distance | `5.0` | Distancia horizontal mantenida |
| Height Deadzone | `0.3` | Diferencia vertical mínima para corregir altura |

### Obstacle Avoidance

| Propiedad | Por defecto | Descripción |
|-----------|-------------|-------------|
| Probes | `6 — Full` | Número de raycasts de evasión: `6 — Full` o `2 — Minimal` (perf) |
| Avoid Radius | `2.0` | Rango de detección de obstáculos |
| Avoid Weight | `1.5` | Peso de la dirección de evasión frente a la de seguimiento |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado (simplificado)

```python
import mathutils as _mu
_dt = Range.logic.deltaTime()
if not hasattr(self, '_drone_rdy'):
    self._drone_rdy = True
    self._drone_vel = _mu.Vector((0.0, 0.0, 0.0))
    self._drone_probes = (
        _mu.Vector(( 1, 0, 0)), _mu.Vector((-1, 0, 0)),
        _mu.Vector(( 0, 1, 0)), _mu.Vector(( 0,-1, 0)),
        _mu.Vector(( 0, 0, 1)), _mu.Vector(( 0, 0,-1)),
    )
_drone_target = self.own.scene.objects.get('player')
# ... Seek hacia target + altura
# ... Raycasts de evasión en 6 direcciones
# _drone_steer = seek_dir + avoid_dir * avoid_weight
self._drone_vel = self._drone_vel.lerp(_drone_steer * 4.0, 0.08)
self.own.applyMovement(self._drone_vel * _dt, False)
# Rotación suave en Yaw hacia el objetivo
```

## Uso típico

### Dron de vigilancia

```
[On Update] → [Drone AI:
                   Target=player
                   Speed=5.0
                   Hover Height=4.0
                   Follow Distance=6.0
                   Avoid Radius=2.5]
```

### Dron de bajo rendimiento (zonas con muchos objetos)

```
[On Update] → [Drone AI:
                   Target=player
                   Speed=3.0
                   Probes=2 — Minimal]
```

## Notas

- El objeto debe tener física **No Collision** o **Ghost** para moverse libremente en 3D.
- `applyMovement` en espacio mundo (`False`) — el dron no hereda rotación del objeto padre.
- `Probes=Minimal` usa solo 2 raycasts (adelante + abajo) para mayor rendimiento.
- Para múltiples drones, cada objeto tiene su propio estado `_drone_*` gracias a `hasattr`.
