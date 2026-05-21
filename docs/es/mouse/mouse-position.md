# Mouse Position

**Tipo:** Dato  
**Categoría:** MOUSE

Devuelve la posición X o Y del cursor del ratón en coordenadas normalizadas [0.0, 1.0].

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Axis | Enum | `X` | `X` = horizontal, `Y` = vertical |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Value | Salida | Dato (Float) |

## Código generado

```python
# Axis = X (almacenado en variable local _ms_x)
_ms_x = Range.logic.mouse.position[0]
# luego la expresión devuelve _ms_x

# Axis = Y
_ms_y = Range.logic.mouse.position[1]
```

## Uso típico

### Controlar un parámetro con posición horizontal del cursor

```
[On Update] → [Set Light Energy:
                   Energy = {Mouse Position: Axis=X}]
```

### Depurar posición del cursor

```
[On Update] → [Print: Message = {Mouse Position: Axis=X}]
```

## Notas

- Idéntico a [Get Cursor Pos](../cursor/get-cursor-pos.md) pero sin la opción de remap. Usa `Get Cursor Pos` si necesitas el rango [-1, 1].
- `(0,0)` es esquina superior izquierda, `(1,1)` es esquina inferior derecha.
