# Logic Gate

**Tipo:** Dato  
**Categoría:** LOGIC

Realiza una operación lógica entre dos valores booleanos y devuelve el resultado.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Operation | Enum | `AND` | `AND`, `OR`, `NOT`, `NAND`, `NOR`, `XOR`, `XNOR` |
| A | Bool | `False` | Primer operando |
| B | Bool | `False` | Segundo operando (ignorado para `NOT`) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| A | Entrada | Dato (Bool) |
| B | Entrada | Dato (Bool) |
| Result | Salida | Dato (Bool) |

## Tabla de operaciones

| Operación | Resultado |
|-----------|-----------|
| AND | `A and B` |
| OR | `A or B` |
| NOT | `not A` |
| NAND | `not (A and B)` |
| NOR | `not (A or B)` |
| XOR | `A != B` |
| XNOR | `A == B` |

## Uso típico

### Mover solo si está vivo y en suelo

```
[On Update] → [Branch:
                  Condition = {Logic Gate:
                                   A = {Compare: health > 0}
                                   B = {Compare: on_ground == True}
                                   Op = AND}]
                  └── True ──► [Player Movement]
```

### Atacar si no está recargando ni en animación

```
[On Update] → [Branch:
                  Condition = {Logic Gate:
                                   A = {is_reloading}
                                   B = {is_animating}
                                   Op = NOR}]
                  └── True ──► [Weapon Fire Executor]
```

## Notas

- Nodo de solo datos — no tiene sockets Exec. Combina su salida con [Branch](branch.md) para bifurcación.
- Para `NOT`, solo se evalúa el socket `A`. El socket `B` es ignorado.
