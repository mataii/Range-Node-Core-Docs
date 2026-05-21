# Get Cursor Pos

**Tipo:** Dato  
**Categoría:** CURSOR

Devuelve la posición actual del cursor del ratón. Por defecto en coordenadas normalizadas [0.0, 1.0]; opcionalmente remapeado a [-1.0, 1.0].

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Axis | Enum | `X` | `X` = horizontal, `Y` = vertical |
| Remap | Bool | `False` | Si es True, convierte de [0,1] a [-1,1] |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Value | Salida | Dato (Float) |

## Código generado

```python
# Axis=X, Remap=False
Range.logic.mouse.position[0]

# Axis=X, Remap=True
Range.logic.mouse.position[0] * 2.0 - 1.0
```

## Uso típico

### Detectar si cursor está en mitad derecha de pantalla

```
[On Update] → [BT Condition: {Get Cursor Pos: Axis=X} > 0.5]
                  └── True ──► [...]
```

### Pasar posición a un shader de UI

```
[On Update] → [BTCustomTask:
                   x = {Get Cursor Pos: Axis=X, Remap=True}
                   y = {Get Cursor Pos: Axis=Y, Remap=True}
                   self.own['cursor_x'] = x
                   self.own['cursor_y'] = y]
```

## Notas

- `mouse.position` es una tupla `(x, y)` donde `(0,0)` es la esquina superior izquierda y `(1,1)` la inferior derecha.
- No actualiza el cursor — es de solo lectura.
