# Random Float

**Tipo:** Dato  
**Categoría:** MATH

Devuelve un número flotante aleatorio en el rango [min, max] usando `random.uniform`.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Min | Float | `0.0` | Valor mínimo (inclusive) |
| Max | Float | `1.0` | Valor máximo (inclusive) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Value | Salida | Dato (Float) |

## Código generado

```python
__import__('random').uniform(0.0, 1.0)
```

## Uso típico

### Pitch aleatorio de sonido

```
[On Collision: Property="pickup"] → [Play Sound:
                                         Pitch = {Random Float: Min=0.9, Max=1.1}]
```

### Posición de spawn aleatoria

```
[On Start] → [BTCustomTask:
                  x = {Random Float: Min=-5.0, Max=5.0}
                  y = {Random Float: Min=-5.0, Max=5.0}
                  self.own.worldPosition = [x, y, 0]]
```

### Cooldown aleatorio entre ataques

```
[On State Enter: FSM ID="ai", State="attack"]
    └── On Enter ──► [BTCustomTask:
                          self._bt_bb['attack_cd'] = {Random Float: Min=1.0, Max=3.0}]
```

## Notas

- Cada llamada genera un valor diferente. Para reproducibilidad, fija la semilla con [Set Random Seed](set-random-seed.md).
- `random.uniform(a, b)` incluye ambos extremos.
