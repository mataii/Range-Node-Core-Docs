# Branch

**Tipo:** Rama (True / False)  
**Categoría:** LOGIC

Bifurcación genérica si/sino. Evalúa la condición conectada al socket `Condition` y redirige la ejecución al camino correspondiente.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Condition | Entrada | Dato (Bool) |
| True | Salida | Exec (condición verdadera) |
| False | Salida | Exec (condición falsa) |

## Código generado

```python
if condition_value:
    #TRUE_PATH#
else:
    #FALSE_PATH#
```

## Uso típico

### Condicionar sobre resultado de Compare

```
[On Update] → [Branch: Condition={Compare: A={score}, B=100, Op=>=}]
                  └── True ──► [Node Broadcast: Subject="level_complete"]
```

### Condicionar sobre Logic Gate

```
[On Update] → [Branch:
                  Condition = {Logic Gate: A={is_alive}, B={is_grounded}, Op=AND}]
                  └── True ──► [Player Movement]
```

### Condicionar sobre propiedad booleana

```
[On Update] → [Branch: Condition={Get Property: prop="is_visible"}]
                  ├── True ──► [...]
                  └── False ──► [...]
```

## Notas

- `Branch` es el nodo if/else genérico. Para comparaciones más específicas, usa [If Property](if-property.md) (que incluye el operador) o [BT Condition](../ai-bt/bt-condition.md) (expresión Python arbitraria).
- La condición puede venir de cualquier nodo de datos que devuelva un valor evaluable como booleano.
