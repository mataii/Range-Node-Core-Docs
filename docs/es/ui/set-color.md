# Set Color

**Tipo:** Acción  
**Categoría:** UI

Cambia el color RGBA de un objeto (`obj.color`). Compatible con objetos de malla y de fuente.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Object | Enum | — | Objeto cuyo color cambiar |
| Color | Color (RGBA) | `(1, 1, 1, 1)` | Color estático (usado si ningún canal está conectado) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| R | Entrada | Dato (Float, 0.0 – 1.0) |
| G | Entrada | Dato (Float, 0.0 – 1.0) |
| B | Entrada | Dato (Float, 0.0 – 1.0) |
| A | Entrada | Dato (Float, 0.0 – 1.0) |
| Out | Salida | Exec |

## Código generado

```python
_col_obj = self.own.scene.objects.get('health_icon')
if _col_obj:
    _col_obj.color = [1.0, 0.2, 0.2, 1.0]
```

## Uso típico

### Parpadeo de daño (rojo al recibir impacto)

```
[On Message: Subject="hit"]
    → [Set Color: Object=player_mesh, R=1.0, G=0.0, B=0.0, A=1.0]
    → [Wait: Frames=10]
    → [Set Color: Object=player_mesh, R=1.0, G=1.0, B=1.0, A=1.0]
```

### Color dinámico según calor

```
[On Update]
    → [Set Color:
           Object=heat_indicator
           R={Math: {Get Property: prop="heat"} / 100.0}
           G={Math: 1.0 - {Get Property: prop="heat"} / 100.0}
           B=0.0
           A=1.0]
```

### Fade de transparencia

```
[On Update]
    → [Set Color:
           Object=ghost_mesh
           R=1.0  G=1.0  B=1.0
           A={Tween Float: ID="fade_out"}]
```

## Notas

- Si ningún canal R/G/B/A está conectado, se usa el selector de color estático del nodo.
- Los valores se esperan en rango `0.0 – 1.0` (lineal).
- El color del objeto (`obj.color`) afecta al material de forma multiplicativa.
