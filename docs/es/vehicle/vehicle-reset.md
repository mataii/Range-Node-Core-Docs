# Vehicle Reset

**Tipo:** Acción  
**Categoría:** VEHICLE

Detecta si el vehículo está volcado (Z local por debajo de un umbral) y lo endereza automáticamente, elevándolo y reseteando su orientación y velocidades.

## Propiedades

| Propiedad | Por defecto | Descripción |
|-----------|-------------|-------------|
| Tilt Threshold | `0.3` | Umbral del eje Z local para detectar vuelco (0 = 90°, 1 = recto) |
| Lift Height | `1.5` | Cuánto elevar el vehículo al resetear (en unidades) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |
| Flipped | Salida | Exec |

## Código generado

```python
if self.own.localOrientation[2][2] < 0.3:
    self.own.worldPosition += mathutils.Vector((0, 0, 1.5))
    self.own.worldOrientation = mathutils.Matrix.Identity(3)
    self.own.linearVelocity   = mathutils.Vector((0, 0, 0))
    self.own.angularVelocity  = mathutils.Vector((0, 0, 0))
```

## Uso típico

### Reset automático al volcar

```
[On Update]
    → [Vehicle Reset: Tilt Threshold=0.3, Lift Height=1.5]
          └── Out     ──► [...]  # continuación normal
          └── Flipped ──► [Set Text: Object=hud_msg, Text="¡Vuelco!"]
```

### Reset manual con tecla R

```
[On Key Press: Key=R, Mode=PRESSED]
    → [Vehicle Reset: Tilt Threshold=0.0]  # siempre resetea
```

## Notas

- El umbral `0.3` detecta volcados cuando el vehículo ha girado más de ~72° desde la vertical.
- La rama `Flipped` se activa solo cuando se aplica el reset, no en cada frame volcado.
- `Vehicle Player` incluye esta funcionalidad como opción (`Auto-Reset on Flip`).
