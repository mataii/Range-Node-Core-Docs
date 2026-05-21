# Raycast

**Tipo:** Acción (rama)  
**Categoría:** RAYCAST

Lanza un rayo desde el objeto en una dirección y ramifica la ejecución según si impacta algo o no. Almacena los resultados por ID para que [Raycast Result](raycast-result.md) los lea.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Ray ID | String | `"ray1"` | Identificador compartido con Raycast Result |
| Direction | Enum | `Forward (-Y)` | Dirección del rayo: Forward, Backward, Up, Down, Right, Left, Custom, To Object |
| Target Object | Object | — | Solo con `Direction = To Object` |
| Distance | Float | `10.0` | Longitud máxima del rayo |
| Prop Filter | String | `""` | Solo impacta objetos con esta propiedad BGE (vacío = todos) |
| X-Ray | Bool | `False` | Si es True, atraviesa objetos que no coincidan con Prop Filter |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Dir X / Dir Y / Dir Z | Entrada | Dato (Float) — solo con Direction=Custom |
| Dist | Entrada | Dato (Float) — sobreescribe Distance |
| Hit | Salida | Exec |
| Miss | Salida | Exec |

## Código generado

```python
import mathutils as _mu
_rc_dir  = (self.own.worldOrientation * _mu.Vector(( 0.0, -1.0,  0.0))).normalized()
_rc_orig = self.own.worldPosition.copy()
_rc_to   = _rc_orig + _rc_dir * float(10.0)
_rc_res  = self.own.rayCast(_rc_to, _rc_orig, float(10.0), '', 0, 0, 0)
self._rc_ray1_hit = _rc_res[0]
self._rc_ray1_pos = list(_rc_res[1]) if _rc_res[1] else [0.0, 0.0, 0.0]
self._rc_ray1_nrm = list(_rc_res[2]) if _rc_res[2] else [0.0, 0.0, 1.0]
if _rc_res[0] is not None:
    # rama Hit
else:
    # rama Miss
```

## Uso típico

### Detectar obstáculo enfrente

```
[On Update] → [Raycast: Direction=Forward, Distance=2.0, Ray ID="obs"]
                  └── Hit  ──► [Send Message: Subject="obstacle_ahead"]
                  └── Miss ──► [...]
```

### Disparar hacia un enemigo

```
[On Key Press: Key=SPACE, Mode=PRESSED]
    → [Raycast: Direction=To Object, Target=enemy, Distance=50.0, Prop Filter="enemy"]
          └── Hit  ──► [Send Message: Subject="take_damage", Body="10"]
          └── Miss ──► [...]
```

## Notas

- Los resultados se almacenan en `self._rc_<ID>_hit`, `self._rc_<ID>_pos`, `self._rc_<ID>_nrm`.
- Usa el mismo `Ray ID` en [Raycast Result](raycast-result.md) para leer los valores.
- Con `Direction=Custom`, conecta los sockets Dir X/Y/Z para controlar la dirección en espacio local.
- El rayo se emite desde el origen del objeto en espacio mundo.
