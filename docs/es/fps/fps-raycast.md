# FPS Raycast

**Tipo:** Acción (rama)  
**Categoría:** FPS

Lanza un rayo desde la cámara activa (o desde el objeto) en el eje configurado. Expone directamente las salidas de objeto impactado, posición y normal — especialmente conveniente para mecánicas de disparo y selección en FPS.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Source | Enum | `Camera` | Origen del rayo: `Camera` (cámara activa FPS) o `Object` (propio objeto) |
| Axis | Enum | `Forward (Y)` | Eje de disparo en espacio local: `Y` (adelante), `X` (derecha), `Z` (arriba) |
| Distance | Float | `10.0` | Longitud máxima del rayo |
| Property Filter | String | `""` | Solo impacta objetos con esta propiedad BGE |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Hit | Salida | Exec |
| Miss | Salida | Exec |
| Object | Salida | Dato (KX_GameObject o None) |
| Position | Salida | Dato (Vector) |
| Normal | Salida | Dato (Vector) |

## Código generado

```python
_ray_src = (self._fps_cam if (hasattr(self, '_fps_cam') and self._fps_cam) else self.own)
_ray_to = _ray_src.worldPosition + _ray_src.worldOrientation * mathutils.Vector((0, 10.0, 0))
_rc_obj, _rc_pos, _rc_norm = self.own.rayCast(_ray_to, _ray_src.worldPosition, 10.0, '', 1, 1)
if _rc_obj:
    # rama Hit
else:
    # rama Miss
```

## Uso típico

### Disparo con daño

```
[On Key Press: Key=SPACE, Mode=PRESSED]
    → [FPS Raycast: Source=Camera, Axis=Y, Distance=50.0, Prop Filter="enemy"]
          └── Hit  ──► [BTCustomTask: _rc_obj['health'] = _rc_obj.get('health', 100) - 25]
          └── Miss ──► [...]
```

### Mostrar nombre del objeto apuntado

```
[On Update]
    → [FPS Raycast: Source=Camera, Axis=Y, Distance=5.0]
          └── Hit  ──► [Set Text: Object=crosshair_label, Text=_rc_obj.name]
          └── Miss ──► [Set Text: Object=crosshair_label, Text=""]
```

## Notas

- `_rc_obj`, `_rc_pos` y `_rc_norm` son accesibles en nodos posteriores dentro del mismo frame.
- Con `Source=Camera`, el nodo busca `self._fps_cam` — establecido por [FPS Tools](fps-tools.md) o [Mouse Look](mouse-look.md).
- Diferente del nodo `Raycast` en la categoría RAYCAST: este no usa Ray ID, emite resultados directamente como variables de ejecución.
