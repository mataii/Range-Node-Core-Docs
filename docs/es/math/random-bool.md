# Random Bool

**Tipo:** Dato  
**Categoría:** MATH

Devuelve `True` con la probabilidad especificada, `False` en caso contrario.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Probability | Float | `0.5` | Probabilidad de True [0.0 – 1.0] |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Value | Salida | Dato (Bool) |

## Código generado

```python
(__import__('random').random() < 0.5)
```

## Uso típico

### Activar evento con probabilidad del 20%

```
[On Update] → [Branch: Condition={Random Bool: Prob=0.2}]
                  └── True ──► [Play Sound: File="sfx/ambient.wav"]
```

### Decidir comportamiento de IA al azar

```
[On State Enter: FSM ID="ai", State="idle"]
    └── On Enter ──► [Branch: Condition={Random Bool: Prob=0.5}]
                         ├── True ──► [Set State: State="patrol"]
                         └── False ──► [Set State: State="wander"]
```

## Notas

- `Probability=0.0` siempre devuelve `False`, `Probability=1.0` siempre devuelve `True`.
- Para bifurcación directa por probabilidad, usa [Random Chance](random-chance.md).
