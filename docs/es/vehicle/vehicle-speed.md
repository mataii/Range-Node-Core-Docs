# Vehicle Speed

**Tipo:** Dato  
**Categoría:** VEHICLE

Devuelve la velocidad actual del chasis del vehículo en metros/segundo y km/h, calculada a partir de `linearVelocity.length`.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| m/s | Salida | Dato (Float) |
| km/h | Salida | Dato (Float) |

## Código generado

```python
_spd_ms  = self.own.linearVelocity.length
_spd_kmh = _spd_ms * 3.6
```

## Uso típico

### Velocímetro en HUD

```
[On Update]
    → [Set Text:
           Object=speedometer
           Text={Number To String:
                     Value={Vehicle Speed: Socket=km/h}
                     Decimals=0
                     Suffix=" km/h"}]
```

### Limitar velocidad máxima

```
[On Update]
    → [BT Condition: {Vehicle Speed: Socket=km/h} < 120]
          └── True ──► [Vehicle Throttle: Force=800]
          └── False ──► [Vehicle Throttle: Force=0]
```

## Notas

- La velocidad es la magnitud del vector de velocidad lineal — siempre positiva.
- Para dirección (avance/retroceso), comprueba el producto escalar con el vector local Y del objeto.
- No requiere `Vehicle Setup` para funcionar — usa directamente `linearVelocity`.
