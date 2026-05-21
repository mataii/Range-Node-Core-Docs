# Transform

**Tipo:** Acción  
**Categoría:** OBJECT

Mueve, rota o escala el objeto. Usa `applyMovement`, `applyRotation` o `localScale` según el modo.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Mode | Enum | `MOVE` | `MOVE`, `ROTATE`, `SCALE` |
| X | Float | `0.0` | Componente X |
| Y | Float | `0.0` | Componente Y |
| Z | Float | `0.0` | Componente Z |
| Local | Bool | `True` | Si es True, aplica en espacio local del objeto |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |
| X | Entrada | Dato (Float) |
| Y | Entrada | Dato (Float) |
| Z | Entrada | Dato (Float) |

## Código generado

```python
# MOVE
self.own.applyMovement([0.0, 0.0, 0.0], True)

# ROTATE (en radianes)
self.own.applyRotation([0.0, 0.0, 0.0], True)

# SCALE (escala absoluta, no relativa)
self.own.localScale = [1.0, 1.0, 1.0]
```

## Uso típico

### Mover objeto hacia adelante

```
[On Update] → [Transform: Mode=MOVE, Y=0.1, Local=True]
```

### Rotar en eje Z cada frame

```
[On Update] → [Transform: Mode=ROTATE, Z=0.05, Local=True]
```

### Escalar objeto a tamaño específico

```
[On Start] → [Transform: Mode=SCALE, X=2.0, Y=2.0, Z=2.0]
```

## Notas

- Para `ROTATE`, los valores están en radianes. 0.05 rad/frame ≈ 3°/frame a 60fps = ~180°/s.
- Para `SCALE`, los valores son escala absoluta — no multiplicativos. Para duplicar tamaño: `[2, 2, 2]`.
- `Local=False` usa coordenadas de mundo para MOVE y ROTATE.
