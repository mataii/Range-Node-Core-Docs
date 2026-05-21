# Format String

**Tipo:** Dato  
**Categoría:** DATA

Formatea una cadena plantilla con hasta 4 valores usando la sintaxis de Python `.format()`.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Template | String | `"{0}"` | Cadena con marcadores `{0}`, `{1}`, `{2}`, `{3}` |
| Value 0 | Any | `""` | Primer valor |
| Value 1 | Any | `""` | Segundo valor |
| Value 2 | Any | `""` | Tercer valor |
| Value 3 | Any | `""` | Cuarto valor |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Value 0–3 | Entrada | Dato |
| Result | Salida | Dato (String) |

## Código generado

```python
"HP: {0} / {1}".format(75, 100)
# resultado: "HP: 75 / 100"
```

## Uso típico

### Mostrar vida y vida máxima

```
[On Update] → [Set Text:
                  Text = {Format String:
                              Template = "HP: {0} / {1}"
                              Value 0 = {Get Property: prop="health"}
                              Value 1 = 100}]
```

### Construir clave de guardado dinámica

```
[BTCustomTask:
    key = {Format String: Template="save_slot_{0}", Value 0={Get Property: prop="slot"}}
    Range.logic.globalDict[key] = data]
```

## Notas

- Usa Python `str.format()` — soporta toda su sintaxis: `{0:.2f}` para 2 decimales, `{0:03d}` para entero con ceros.
- Los valores no usados (sin marcador en el template) son ignorados.
