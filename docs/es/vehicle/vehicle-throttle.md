# Vehicle Throttle

**Tipo:** Acción  
**Categoría:** VEHICLE

Aplica fuerza de motor a las ruedas motrices del vehículo. Requiere que [Vehicle Setup](vehicle-setup.md) haya sido ejecutado previamente.

## Propiedades

| Propiedad | Por defecto | Descripción |
|-----------|-------------|-------------|
| Force | `500.0` | Fuerza de motor (positivo = avanzar, negativo = marcha atrás) |
| Drive | `RWD` | Tracción: `AWD` (4 ruedas), `RWD` (traseras), `FWD` (delanteras) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Force | Entrada | Dato (Float) |
| Out | Salida | Exec |

## Código generado

```python
# Drive = RWD
if hasattr(self, '_vehicle'):
    for _ti in [2, 3]:
        if _ti < self._vehicle.getNumWheels():
            self._vehicle.applyEngineForce(500.0, _ti)
```

## Uso típico

### Aceleración con W

```
[On Key Press: Key=W, Mode=HELD]
    → [Vehicle Throttle: Force=800, Drive=RWD]

[On Key Press: Key=S, Mode=HELD]
    → [Vehicle Throttle: Force=-400, Drive=RWD]
```

### Fuerza variable (sensor de eje de gamepad)

```
[On Update]
    → [Vehicle Throttle:
           Force={Math: {Gamepad Axis: Axis=Left Y} * 800.0}
           Drive=AWD]
```

## Notas

- Valores negativos aplican fuerza hacia atrás (marcha atrás).
- Para frenar, usa [Vehicle Brake](vehicle-brake.md) en lugar de fuerza negativa.
- Aplicar `0.0` de fuerza elimina la aceleración sin frenar.
