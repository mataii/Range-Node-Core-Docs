# Look At

**Tipo:** Acción  
**Categoría:** OBJECT

Rota el objeto gradualmente para que mire hacia un objetivo usando interpolación esférica (slerp). Permite bloquear el eje Z para rotación solo horizontal.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Target | String | `"target"` | Nombre del objeto objetivo |
| Track Axis | Enum | `Y` | Eje del objeto que apunta al objetivo: `X`, `Y`, `Z`, `-X`, `-Y`, `-Z` |
| Up Axis | Enum | `Z` | Eje que se mantiene "arriba": `X`, `Y`, `Z` |
| Speed | Float | `0.1` | Factor de interpolación [0.0 = sin movimiento, 1.0 = instantáneo] |
| Lock Z | Bool | `False` | Si es True, ignora la diferencia de altura (rotación plana) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
_lat = scene.objects['target']
_la_dir = _lat.worldPosition - self.own.worldPosition
if True:  # lock_z
    _la_dir[2] = 0.0
if _la_dir.length > 0.001:
    _la_rot = _la_dir.to_track_quat('Y', 'Z')
    self.own.worldOrientation = self.own.worldOrientation.slerp(_la_rot.to_matrix(), 0.1)
```

## Uso típico

### Torreta que sigue al jugador

```
[On Update] → [Look At: Target="Player", Track Axis=Y, Speed=0.05, Lock Z=True]
```

### Enemigo que mira hacia el jugador en 3D

```
[On Update] → [On State: FSM ID="ai", State="aim"]
                  └── True ──► [Look At: Target="Player", Speed=0.15]
```

### Flecha de UI que apunta a objetivo

```
[On Update] → [Look At: Target="objective_marker", Track Axis=-Y, Speed=1.0]
```

## Notas

- `Lock Z=True` es útil para personajes y torretas de suelo que no deben inclinarse al seguir objetos en alturas diferentes.
- Con `Speed=1.0` la rotación es instantánea (sin interpolación).
- Si el objetivo está exactamente en la misma posición, la función retorna sin hacer nada (guarda contra vector cero).
