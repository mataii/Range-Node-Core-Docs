# Concat String

**Tipo:** Dato  
**Categoría:** DATA

Concatena dos cadenas de texto con un separador opcional.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| A | String | `""` | Primera cadena |
| B | String | `""` | Segunda cadena |
| Separator | String | `""` | Texto entre A y B |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| A | Entrada | Dato (String) |
| B | Entrada | Dato (String) |
| Result | Salida | Dato (String) |

## Código generado

```python
str("Hello") + " " + str("World")
# resultado: "Hello World"
```

## Uso típico

### Construir nombre de archivo dinámico

```
[On Start] → [Play Sound:
                 Sound File = {Concat String: A="music/", B={Get Property: prop="zone"}, Separator=""}]
```

### Mostrar puntuación formateada

```
[On Update] → [Set Text:
                  Text = {Concat String: A="Score: ", B={Get Property: prop="score"}}]
```

## Notas

- Aplica `str()` a ambos operandos, por lo que acepta cualquier tipo que Python pueda convertir a cadena.
- Para formateo más complejo, usa [Format String](format-string.md).
