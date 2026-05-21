# Mouse Raycast

**Tipo:** Rama (Hit / Miss)  
**Categoría:** MOUSE

Lanza un rayo desde la cámara activa a través de la posición del cursor. Expone el objeto golpeado, la posición de impacto y la normal de la superficie.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Ray ID | String | `"mouse"` | Identificador para acceder a los resultados después |
| Distance | Float | `100.0` | Distancia máxima del rayo |
| Prop Filter | String | `""` | Solo detecta objetos con esta propiedad BGE |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Hit | Salida | Exec (impacto detectado) |
| Miss | Salida | Exec (sin impacto) |
| Object | Salida | Dato (objeto golpeado) |
| Position | Salida | Dato (Vector posición de impacto) |
| Normal | Salida | Dato (Vector normal de superficie) |

## Código generado

```python
_mrc_cam = scene.active_camera
_mrc_hit, _mrc_pos, _mrc_nrm = _mrc_cam.getScreenRay(
    Range.logic.mouse.position[0],
    Range.logic.mouse.position[1],
    100.0,
    ''
) or (None, None, None)
self._rc_mouse_hit = _mrc_hit
self._rc_mouse_pos = _mrc_pos
self._rc_mouse_nrm = _mrc_nrm
if _mrc_hit:
    #HIT_PATH#
else:
    #MISS_PATH#
```

## Uso típico

### Instanciar objeto en punto de click

```
[On Update] → [Mouse Button: Button=Left, Mode=activated]
                  └── True ──► [Mouse Raycast: Ray ID="place"]
                                    └── Hit ──► [Add Object:
                                                     Object = "building"
                                                     Position = {Position socket}]
```

### Apuntar arma hacia el cursor

```
[On Update] → [Mouse Raycast: Ray ID="aim"]
                  └── Hit ──► [BTCustomTask:
                                   target = {Position socket}
                                   # orientar arma hacia target...]
```

## Notas

- Los resultados se almacenan en `self._rc_<ray_id>_hit/pos/nrm` para acceso desde [Raycast Result](../raycast/raycast-result.md) si se necesita más tarde.
- `getScreenRay` devuelve una tupla `(object, position, normal)` o `(None, None, None)` sin impacto.
