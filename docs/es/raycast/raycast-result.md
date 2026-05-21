# Raycast Result

**Tipo:** Dato  
**Categoría:** RAYCAST

Lee el resultado almacenado por un nodo [Raycast](raycast.md), [General Raycast](general-raycast.md) o [Mouse Pick](mouse-pick.md) con el mismo Ray ID. Ambos nodos deben estar en el mismo objeto.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Ray ID | String | `"ray1"` | Debe coincidir con el nodo de raycast |
| Result | Enum | `Hit Name` | Qué dato devolver (ver tabla) |

### Opciones de Result

| Valor | Tipo devuelto | Descripción |
|-------|---------------|-------------|
| `Hit Name` | String | Nombre del objeto impactado (vacío si no hay impacto) |
| `Hit (bool)` | Bool | True si hubo impacto |
| `Hit Pos X/Y/Z` | Float | Coordenada X, Y o Z del punto de impacto |
| `Normal X/Y/Z` | Float | Componente X, Y o Z de la normal de la superficie |
| `Distance` | Float | Distancia desde el origen del rayo al punto de impacto |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Value | Salida | Dato (tipo según Result) |

## Código generado

```python
# Result = Hit Name
(getattr(self, '_rc_ray1_hit', None).name if getattr(self, '_rc_ray1_hit', None) else '')

# Result = Hit (bool)
(getattr(self, '_rc_ray1_hit', None) is not None)

# Result = Hit Pos X
(getattr(self, '_rc_ray1_pos', [0.0, 0.0, 0.0])[0])

# Result = Distance
((lambda _hp: ((_hp[0]-self.own.worldPosition.x)**2 + ...)**0.5
  if getattr(self, '_rc_ray1_hit', None) else 0.0)
 (getattr(self, '_rc_ray1_pos', [0.0, 0.0, 0.0])))
```

## Uso típico

### Mostrar nombre del objeto apuntado

```
[On Update]
    → [Raycast: Direction=Forward, Distance=5.0, Ray ID="aim"]
          └── Hit  ──► [Set Text: Text={Raycast Result: Ray ID="aim", Result=Hit Name}]
          └── Miss ──► [Set Text: Text=""]
```

### Spawnear partículas en el punto de impacto

```
[On Key Press: Key=SPACE, Mode=PRESSED]
    → [Raycast: Direction=Forward, Distance=20.0, Ray ID="shot"]
          └── Hit  ──► [Add Object:
                           Object="spark_particle"
                           X={Raycast Result: Ray ID="shot", Result=Hit Pos X}
                           Y={Raycast Result: Ray ID="shot", Result=Hit Pos Y}
                           Z={Raycast Result: Ray ID="shot", Result=Hit Pos Z}]
          └── Miss ──► [...]
```

## Notas

- Este nodo NO ejecuta ningún raycast — solo lee lo que almacenó el nodo ejecutor.
- El raycast debe ejecutarse en el mismo frame para que el resultado esté actualizado.
- Si el raycast nunca se ejecutó, `Hit (bool)` devuelve `False` y las posiciones devuelven `[0,0,0]`.
