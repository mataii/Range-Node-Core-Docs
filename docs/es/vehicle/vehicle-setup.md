# Vehicle Setup

**Tipo:** Acción  
**Categoría:** VEHICLE

Inicializa el constraint de vehículo de Range Game Engine y registra las cuatro ruedas con sus parámetros de suspensión y fricción. Solo se ejecuta en el primer frame (guard `hasattr`).

## Propiedades

| Propiedad | Por defecto | Descripción |
|-----------|-------------|-------------|
| Front Left | — | Objeto rueda delantera izquierda |
| Front Right | — | Objeto rueda delantera derecha |
| Rear Left | — | Objeto rueda trasera izquierda |
| Rear Right | — | Objeto rueda trasera derecha |
| Wheel Radius | `0.35` | Radio de la rueda en unidades |
| Susp. Length | `0.4` | Longitud de la suspensión |
| Stiffness | `50.0` | Rigidez de la suspensión |
| Damping | `10.0` | Amortiguamiento de la suspensión |
| Compression | `4.0` | Compresión de la suspensión |
| Tyre Friction | `4.0` | Fricción del neumático |
| Roll Influence | `0.05` | Influencia del roll (0 = sin vuelco, 1 = máximo) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
if not hasattr(self, '_veh_ready'):
    _veh_scene = Range.logic.getCurrentScene()
    self._vehicle = Range.constraints.createVehicle(self.own.getPhysicsId())
    if 'wheel_fl' in _veh_scene.objects:
        _w = _veh_scene.objects['wheel_fl']
        _side = -1.0 if _w.localPosition.x <= 0 else 1.0
        self._vehicle.addWheel(_w, _w.localPosition,
            mathutils.Vector((0, 0, -1)),
            mathutils.Vector((_side, 0, 0)),
            0.4, 0.35, True)
    # ... (demás ruedas)
    for _wi in range(self._vehicle.getNumWheels()):
        self._vehicle.setTyreFriction(_wi, 4.0)
        self._vehicle.setSuspensionStiffness(_wi, 50.0)
        self._vehicle.setSuspensionDamping(_wi, 10.0)
        self._vehicle.setSuspensionCompression(_wi, 4.0)
        self._vehicle.setRollInfluence(_wi, 0.05)
    self._veh_ready = True
```

## Uso típico

```
[On Update]
    → [Vehicle Setup: FL=rueda_di, FR=rueda_dd, RL=rueda_ti, RR=rueda_td
           Wheel Radius=0.35, Tyre Friction=4.5, Stiffness=60]
    → [Vehicle Throttle: ...]
    → [Vehicle Steering: ...]
```

## Notas

- El objeto del componente debe tener física **Vehicle** en Range Game Engine.
- Las ruedas se registran según la posición local — las que tienen `localPosition.x ≤ 0` se tratan como izquierda.
- `Roll Influence` bajo (0.05) evita vuelcos fáciles; aumentarlo da física más realista.
