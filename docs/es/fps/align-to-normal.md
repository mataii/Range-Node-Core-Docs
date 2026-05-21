# Align To Normal

**Tipo:** Acción  
**Categoría:** FPS

Alinea el objeto que tiene el componente a la normal de la superficie del último raycast (`_rc_norm`). Debe ejecutarse en la rama **Hit** de [FPS Raycast](fps-raycast.md).

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Up Axis | Enum | `Z` | Eje del objeto que apuntará en la dirección de la normal: `Z`, `Y` o `X` |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
if '_rc_norm' in dir() and _rc_norm:
    self.own.worldOrientation = _rc_norm.to_track_quat('Z', 'Y').to_matrix()
```

## Uso típico

### Objeto que se adhiere a la superficie apuntada

```
[On Update]
    → [FPS Raycast: Source=Camera, Axis=Y, Distance=5.0, Prop Filter="sticky"]
          └── Hit  ──► [Align To Normal: Up Axis=Z]
          └── Miss ──► [...]
```

### Personaje sobre superficie inclinada

```
[On Update]
    → [FPS Raycast: Source=Object, Axis=Z, Distance=1.5]  # rayo hacia abajo
          └── Hit  ──► [Align To Normal: Up Axis=Z]
          └── Miss ──► [...]
```

## Notas

- Solo funciona si `_rc_norm` existe en el scope de ejecución actual — usa [FPS Raycast](fps-raycast.md) en el mismo frame.
- Para alinear un objeto spawneado (no el portador del componente), usa [Spawn At Hit](spawn-at-hit.md) con `Align to Normal=True`.
