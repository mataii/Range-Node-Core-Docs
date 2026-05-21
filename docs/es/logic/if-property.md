# If Property

**Tipo:** Rama (True / False)  
**Categoría:** LOGIC

Compara una propiedad BGE del objeto con un valor usando uno de 6 operadores de comparación.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Property | String | `"health"` | Nombre de la propiedad BGE |
| Operator | Enum | `==` | `==`, `!=`, `<`, `<=`, `>`, `>=` |
| Value | Any | `0` | Valor de referencia para comparar |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Value | Entrada | Dato |
| True | Salida | Exec (condición verdadera) |
| False | Salida | Exec (condición falsa) |

## Código generado

```python
if self.own.get('health', None) <= 0:
    #TRUE_PATH#
else:
    #FALSE_PATH#
```

## Uso típico

### Detectar muerte del jugador

```
[On Update] → [If Property: prop="health", Op=<=, Value=0]
                  └── True ──► [Set State: State="dead"]
```

### Verificar que el inventario no está lleno

```
[On Collision: Property="pickup"] → [If Property: prop="item_count", Op=<, Value=10]
                                        └── True ──► [BTCustomTask: add_item()]
```

### Comprobar si el jugador está en zona

```
[On Update] → [If Property: prop="in_safe_zone", Op===, Value=True]
                  └── True ──► [...]
```

## Notas

- Usa `self.own.get(prop, None)` — si la propiedad no existe, el valor es `None`, lo que puede causar `TypeError` con operadores `<`, `<=`, `>`, `>=`.
- Para comparaciones de cadenas, usa `==` o `!=`. Los demás operadores pueden producir resultados inesperados.
