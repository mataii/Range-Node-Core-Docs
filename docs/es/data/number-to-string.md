# Number To String

**Tipo:** Dato  
**Categoría:** DATA

Convierte un número a cadena de texto con prefijo, sufijo y control de decimales opcionales.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Value | Float | `0.0` | Número a convertir |
| Prefix | String | `""` | Texto antes del número |
| Suffix | String | `""` | Texto después del número |
| Decimals | Int | `2` | Número de decimales. `0` = sin decimales |
| As Int | Bool | `False` | Si es True, convierte a entero antes de formatear |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Value | Entrada | Dato (Float/Int) |
| Result | Salida | Dato (String) |

## Código generado

```python
# Decimals=2, Prefix="$", Suffix=" USD"
"$" + f"{42.5:.2f}" + " USD"
# resultado: "$42.50 USD"

# As Int=True
"$" + str(int(42.5)) + " USD"
# resultado: "$42 USD"
```

## Uso típico

### Mostrar velocidad en km/h

```
[On Update] → [Set Text:
                  Text = {Number To String:
                              Value = {Vehicle Speed: km/h}
                              Suffix = " km/h"
                              Decimals = 0}]
```

### Mostrar porcentaje de salud

```
[On Update] → [Set Text:
                  Text = {Number To String:
                              Value = {Get Property: prop="health"}
                              Suffix = "%"
                              As Int = True}]
```

## Notas

- Con `Decimals=0` y `As Int=False` muestra el float sin decimales pero puede generar `42.0` dependiendo de la implementación. Usa `As Int=True` para garantizar un entero limpio.
