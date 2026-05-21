# Get Mouse Delta

**Tipo:** Dato  
**Categoría:** CURSOR

Devuelve el delta X o Y calculado por [Capture Mouse](capture-mouse.md) en el frame actual. Debe usarse en el mismo objeto que ejecuta `Capture Mouse`.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Capture ID | String | `"cam"` | Debe coincidir con el ID de `Capture Mouse` |
| Axis | Enum | `X` | `X` = horizontal, `Y` = vertical |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Delta | Salida | Dato (Float) |

## Código generado

```python
# Axis = X
getattr(self, '_mc_cam_dx', 0.0)

# Axis = Y
getattr(self, '_mc_cam_dy', 0.0)
```

## Uso típico

### Aplicar rotación de mouselook

```
[On Update] → [BTCustomTask:
                   dx = {Get Mouse Delta: Capture ID="cam", Axis=X}
                   dy = {Get Mouse Delta: Capture ID="cam", Axis=Y}
                   self.own.applyRotation([0, 0, -dx], False)]
```

## Notas

- Si `Capture Mouse` no ha corrido aún, devuelve `0.0` (el atributo no existe; `getattr` usa el fallback).
- El ID debe ser exactamente igual al de `Capture Mouse` — incluyendo mayúsculas y minúsculas.
