# Random Int

**Tipo:** Dato  
**Categoría:** MATH

Devuelve un número entero aleatorio en el rango [min, max] usando `random.randint`.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Min | Int | `0` | Valor mínimo (inclusive) |
| Max | Int | `10` | Valor máximo (inclusive) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Value | Salida | Dato (Int) |

## Código generado

```python
__import__('random').randint(0, 10)
```

## Uso típico

### Seleccionar frase aleatoria de diálogo

```
[On Start] → [Set Property: prop="line_idx", Value={Random Int: Min=0, Max=4}]
```

### Daño aleatorio

```
[Weapon Fire Executor] → [BTCustomTask:
                              dmg = {Random Int: Min=8, Max=15}
                              target.own['health'] -= dmg]
```

### Spawn de objeto en posición aleatoria de una cuadrícula

```
[On Start] → [BTCustomTask:
                  col = {Random Int: Min=0, Max=7}
                  row = {Random Int: Min=0, Max=7}
                  self.own.worldPosition = [col * 2.0, row * 2.0, 0]]
```

## Notas

- `randint(a, b)` incluye ambos extremos (`a <= N <= b`).
- Para flotantes, usa [Random Float](random-float.md).
