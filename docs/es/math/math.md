# Math

**Tipo:** Dato  
**Categoría:** MATH

Realiza una operación aritmética entre dos valores A y B.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Operation | Enum | `ADD` | `ADD`, `SUB`, `MUL`, `DIV` |
| A | Float | `0.0` | Primer operando |
| B | Float | `1.0` | Segundo operando |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| A | Entrada | Dato (Float) |
| B | Entrada | Dato (Float) |
| Result | Salida | Dato (Float) |

## Código generado

```python
# ADD
(A + B)

# SUB
(A - B)

# MUL
(A * B)

# DIV
(A / B) if B != 0 else 0
```

La división incluye guardia contra cero.

## Uso típico

### Calcular progreso normalizado

```
[On Update] → [Set Bar:
                  Value = {Math: A={current}, B={total}, Op=DIV}]
```

### Escalar velocidad

```
[Player Movement: Speed = {Math: A={base_speed}, B={sprint_mult}, Op=MUL}]
```

### Calcular salud restante como porcentaje

```
[On Update] → [Set Text:
                  Text = {Number To String:
                              Value = {Math: A={health}, B=100, Op=MUL}}]
```

## Notas

- Nodo de solo datos — no tiene sockets Exec.
- La división entre cero devuelve `0` en lugar de lanzar `ZeroDivisionError`.
