# General Raycast

**Tipo:** Acción (rama)  
**Categoría:** RAYCAST

Lanza un rayo entre dos puntos arbitrarios en espacio mundo. Más flexible que [Raycast](raycast.md) porque permite definir el origen y destino mediante sockets.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Ray ID | String | `"grc1"` | Identificador compartido con Raycast Result |
| Distance | Float | `10.0` | Longitud máxima (usada cuando el socket To está desconectado) |
| Prop Filter | String | `""` | Solo impacta objetos con esta propiedad BGE |
| X-Ray | Bool | `False` | Atraviesa objetos que no coincidan con Prop Filter |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| From X / From Y / From Z | Entrada | Dato (Float) — origen del rayo (desconectado = posición del objeto) |
| To X / To Y / To Z | Entrada | Dato (Float) — destino del rayo (desconectado = adelante × Distance) |
| Hit | Salida | Exec |
| Miss | Salida | Exec |

## Código generado

```python
import mathutils as _mu
_grc_from = self.own.worldPosition.copy()
_grc_to   = _grc_from + (self.own.worldOrientation * _mu.Vector((0.0, -1.0, 0.0))).normalized() * 10.0
_grc_dist = 10.0
_grc_res  = self.own.rayCast(_grc_to, _grc_from, _grc_dist, '', 0, 0, 0)
self._rc_grc1_hit = _grc_res[0]
self._rc_grc1_pos = list(_grc_res[1]) if _grc_res[1] else [0.0, 0.0, 0.0]
self._rc_grc1_nrm = list(_grc_res[2]) if _grc_res[2] else [0.0, 0.0, 1.0]
if _grc_res[0] is not None:
    # rama Hit
else:
    # rama Miss
```

## Uso típico

### Rayo entre dos objetos en la escena

```
[On Update]
    → [General Raycast:
           From X={Object Position: obj=emitter, Axis=X}
           From Y={Object Position: obj=emitter, Axis=Y}
           From Z={Object Position: obj=emitter, Axis=Z}
           To X={Object Position: obj=target, Axis=X}
           To Y={Object Position: obj=target, Axis=Y}
           To Z={Object Position: obj=target, Axis=Z}
           Ray ID="line_of_sight"]
          └── Hit  ──► [...]  # hay algo entre emitter y target
          └── Miss ──► [...]  # línea de visión libre
```

### Rayo desde el suelo hacia arriba (comprobación de techo)

```
[On Update]
    → [General Raycast:
           From X={…} From Y={…} From Z={…}
           To Z={Math: {Object Position: Axis=Z} + 2.0}
           Ray ID="ceiling"]
          └── Hit  ──► [Send Message: Subject="ceiling_hit"]
          └── Miss ──► [...]
```

## Notas

- Si los sockets `From` están desconectados, el origen es la posición del objeto.
- Si los sockets `To` están desconectados, el destino es `From + forward × Distance`.
- Almacena resultados en `self._rc_<ID>_hit/pos/nrm`, igual que [Raycast](raycast.md).
