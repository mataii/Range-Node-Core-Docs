# String Equal

**Tipo:** Rama (Equal / Not Equal)  
**Categoría:** DATA

Compara dos cadenas de texto por igualdad exacta.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| A | String | `""` | Primera cadena |
| B | String | `""` | Segunda cadena a comparar |
| Case Sensitive | Bool | `True` | Si es False, ignora mayúsculas/minúsculas |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| A | Entrada | Dato (String) |
| Equal | Salida | Exec (cadenas iguales) |
| Not Equal | Salida | Exec (cadenas distintas) |

## Código generado

```python
# Case Sensitive = True
if str("hello") == str("hello"):
    #EQUAL_PATH#
else:
    #NOT_EQUAL_PATH#

# Case Sensitive = False
if str("hello").lower() == str("Hello").lower():
    #EQUAL_PATH#
```

## Uso típico

### Comprobar nombre de objeto

```
[On Collision] → [String Equal: A={self.own.name}, B="Player"]
                     └── Equal ──► [...]
```

### Comprobar valor de propiedad string

```
[On Update] → [String Equal:
                  A = {Get Property: prop="mode"}
                  B = "combat"
                  Case Sensitive = False]
                  └── Equal ──► [...]
```

## Notas

- Para solo comprobar si un valor está en una lista, usa `BT Condition` con `in` operator directamente en `BT Custom Task`.
- `Case Sensitive = False` convierte ambas cadenas a minúsculas antes de comparar.
