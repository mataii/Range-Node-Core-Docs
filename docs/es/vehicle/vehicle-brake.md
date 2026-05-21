# Vehicle Brake

**Tipo:** Acción  
**Categoría:** VEHICLE

Aplica fuerza de freno a las ruedas especificadas del vehículo.

## Propiedades

| Propiedad | Por defecto | Descripción |
|-----------|-------------|-------------|
| Force | `20.0` | Fuerza de frenado (mayor = frenado más brusco) |
| Wheels | `All` | Ruedas afectadas: `All`, `Front`, `Rear` |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Force | Entrada | Dato (Float) |
| Out | Salida | Exec |

## Código generado

```python
if hasattr(self, '_vehicle'):
    for _bi in range(4):
        if _bi < self._vehicle.getNumWheels():
            self._vehicle.applyBraking(20.0, _bi)
```

## Uso típico

### Freno de mano (espacio)

```
[On Key Press: Key=SPACE, Mode=HELD]
    → [Vehicle Brake: Force=25, Wheels=All]

[On Key Release: Key=SPACE]
    → [Vehicle Brake: Force=0, Wheels=All]  # soltar freno
```

### Freno trasero para derrapes

```
[On Key Press: Key=SPACE, Mode=HELD]
    → [Vehicle Brake: Force=30, Wheels=Rear]
```

## Notas

- Aplicar `Force=0` libera el freno de esa rueda.
- Combinar `Vehicle Throttle` con `Force=0` y `Vehicle Brake` con fuerza alta da frenada efectiva.
