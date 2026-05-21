# Ground Check

**Tipo:** Acción (rama)  
**Categoría:** RAYCAST

Lanza un rayo hacia abajo desde el objeto para comprobar si está apoyado en el suelo. Diseñado para controladores de personaje y plataformeros.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Max Distance | Float | `1.1` | Hasta dónde bajar el rayo desde el origen |
| Origin Offset Z | Float | `0.0` | Desplazamiento vertical del punto de inicio del rayo |
| Prop Filter | String | `""` | Solo detecta objetos con esta propiedad BGE |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Grounded | Salida | Exec |
| Airborne | Salida | Exec |

## Código generado

```python
_gc_orig    = self.own.worldPosition.copy()
_gc_orig.z += 0.0
_gc_to      = _gc_orig.copy()
_gc_to.z   -= 1.1
_gc_res     = self.own.rayCast(_gc_to, _gc_orig, 1.1, '', 0, 0, 0)
self._ground_hit  = _gc_res[0]
self._ground_pos  = list(_gc_res[1]) if _gc_res[1] else [0.0, 0.0, 0.0]
self._ground_nrm  = list(_gc_res[2]) if _gc_res[2] else [0.0, 0.0, 1.0]
self._ground_dist = ((_gc_res[1] - _gc_orig).length if _gc_res[1] else 1.1)
if _gc_res[0] is not None:
    # rama Grounded
else:
    # rama Airborne
```

## Uso típico

### Control de salto (solo saltar en suelo)

```
[On Key Press: Key=SPACE, Mode=PRESSED]
    → [Ground Check: Max Distance=1.1]
          └── Grounded ──► [Apply Force: Z=5.0, Local=False]
          └── Airborne ──► [...]  # ignorar
```

### Animación de estado (correr / caer)

```
[On Update]
    → [Ground Check: Max Distance=1.2]
          └── Grounded ──► [Play Action: Action="run_anim"]
          └── Airborne ──► [Play Action: Action="fall_anim"]
```

### Leer la distancia al suelo en un script

```python
# Disponible como self._ground_dist después de ejecutar Ground Check
dist_to_ground = self._ground_dist
```

## Notas

- El resultado queda en `self._ground_hit`, `self._ground_pos`, `self._ground_nrm`, `self._ground_dist`.
- Ajusta `Origin Offset Z` si el pivote del objeto no está en su base (p. ej. personaje con pivote en el centro).
- Para suelos con ciertas propiedades usa `Prop Filter="ground"` y añade esa propiedad en el editor de objeto.
