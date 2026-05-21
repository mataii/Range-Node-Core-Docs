# Vehicle Steering

**Tipo:** Acción  
**Categoría:** VEHICLE

Establece el ángulo de dirección de las ruedas delanteras del vehículo.

## Propiedades

| Propiedad | Por defecto | Descripción |
|-----------|-------------|-------------|
| Angle (rad) | `0.0` | Ángulo de giro en radianes (negativo = izquierda, positivo = derecha) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Angle | Entrada | Dato (Float, en radianes) |
| Out | Salida | Exec |

## Código generado

```python
if hasattr(self, '_vehicle'):
    for _si in [0, 1]:
        if _si < self._vehicle.getNumWheels():
            self._vehicle.setSteeringValue(0.5, _si)
```

## Uso típico

### Dirección con teclas A/D

```
[On Key Press: Key=A, Mode=HELD] → [Vehicle Steering: Angle=0.5]
[On Key Press: Key=D, Mode=HELD] → [Vehicle Steering: Angle=-0.5]
[On Key Release: Key=A]          → [Vehicle Steering: Angle=0.0]
[On Key Release: Key=D]          → [Vehicle Steering: Angle=0.0]
```

### Dirección con eje de gamepad

```
[On Update]
    → [Vehicle Steering:
           Angle={Math: {Gamepad Axis: Axis=Left X} * -0.5}]
```

## Notas

- El rango típico es `-0.5` a `0.5` radianes (~29°). Valores mayores pueden causar comportamiento errático.
- Solo afecta a las ruedas delanteras (índices 0 y 1).
- Para retorno progresivo de la dirección al centro, usa `Vehicle Player` que incluye `Steer Return`.
