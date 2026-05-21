# Compare

**Tipo:** Dato  
**Categoría:** LOGIC

Compara dos valores y devuelve un booleano. Versión de datos de `If Property` — no tiene sockets Exec.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Operator | Enum | `==` | `==`, `!=`, `<`, `<=`, `>`, `>=` |
| A | Any | `0` | Primer operando |
| B | Any | `0` | Segundo operando |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| A | Entrada | Dato |
| B | Entrada | Dato |
| Result | Salida | Dato (Bool) |

## Código generado

```python
(self.own.get('health', None) <= 0)
```

Expresión pura que se inserta donde se conecte el socket.

## Uso típico

### Condición compuesta con Logic Gate

```
[On Update] → [Branch:
                  Condition = {Logic Gate:
                                   A = {Compare: A={health}, B=0, Op=>}
                                   B = {Compare: A={ammo}, B=0, Op=>}
                                   Op = AND}]
                  └── True ──► [Player Movement]
```

### Pasar booleano a otro nodo

```
[BT Set Blackboard: can_attack = {Compare: A={health}, B=20, Op=>=}]
```

## Notas

- Nodo de solo datos — no tiene sockets Exec.
- Para bifurcación directa sin nodo `Branch` adicional, usa [If Property](if-property.md) o [BT Condition](../ai-bt/bt-condition.md).
