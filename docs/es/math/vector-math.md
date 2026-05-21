# Vector Math

**Tipo:** Dato  
**Categoría:** MATH

Realiza una operación matemática entre dos vectores 3D y devuelve una componente del resultado o un escalar.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Operation | Enum | `ADD` | Ver tabla de operaciones |
| A | Vector | `[0,0,0]` | Primer vector |
| B | Vector | `[0,0,0]` | Segundo vector (ignorado en LEN y NORM) |
| Component | Enum | `X` | Componente a devolver: `X`, `Y`, `Z`, o `W` para escalares (DOT, LEN) |

## Operaciones disponibles

| Operación | Resultado |
|-----------|-----------|
| ADD | `A + B` |
| SUB | `A - B` |
| MUL | Multiplicación componente a componente |
| SCALE | `A * B[0]` (escalar por primer elemento de B) |
| DOT | Producto punto (escalar) |
| CROSS | Producto vectorial |
| LEN | Longitud de A (escalar) |
| NORM | A normalizado |
| LERP | Interpolación lineal entre A y B con factor B[3] |
| REFLECT | Reflexión de A sobre la normal B |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| A | Entrada | Dato (Vector) |
| B | Entrada | Dato (Vector) |
| Result | Salida | Dato (Float) |

## Uso típico

### Calcular longitud de velocidad

```
[On Update] → [BT Condition: {Vector Math: A={linearVelocity}, Op=LEN} > 5.0]
                  └── True ──► [...]  # objeto en movimiento rápido
```

### Producto punto para ángulo de ataque

```
[On Update] → [BTCustomTask:
                   dot = {Vector Math: A={my_forward}, B={to_player_dir}, Op=DOT}
                   # dot > 0.7 ≈ frente a mí (≈ 45°)]
```

## Notas

- Para acceder al escalar de DOT o LEN, usa `Component=W` (convención del nodo para el resultado escalar).
- Los vectores de entrada pueden venir de propiedades BGE, `worldPosition`, `linearVelocity`, etc.
