# String Contains

**Tipo:** Rama (Found / Not Found)  
**Categoría:** DATA

Detecta si una cadena contiene otra como subcadena.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Text | String | `""` | Cadena donde buscar |
| Substring | String | `""` | Cadena a buscar dentro de Text |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Text | Entrada | Dato (String) |
| Found | Salida | Exec (subcadena encontrada) |
| Not Found | Salida | Exec (subcadena no encontrada) |

## Código generado

```python
if "hello" in str("hello world"):
    #FOUND_PATH#
else:
    #NOT_FOUND_PATH#
```

## Uso típico

### Comprobar etiqueta en nombre de objeto

```
[On Update] → [String Contains:
                  Text = {Get Property: prop="tag"}
                  Substring = "enemy"]
                  └── Found ──► [...]
```

### Filtrar mensajes por asunto

```
[On Message: Subject="cmd_*"] → [String Contains:
                                      Text = {Get Property: prop="msg"}
                                      Substring = "fire"]
                                      └── Found ──► [Weapon Fire Executor]
```

## Notas

- La búsqueda distingue mayúsculas y minúsculas. `"Hello"` no contiene `"hello"`.
- Para comparación exacta, usa [String Equal](string-equal.md).
