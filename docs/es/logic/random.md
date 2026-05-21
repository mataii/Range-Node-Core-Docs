# Random

**Tipo:** Dato  
**Categoría:** LOGIC

Devuelve un número flotante aleatorio en el rango [0.0, 1.0). Nodo legacy — para casos más completos usa los nodos de la categoría MATH (RandomFloat, RandomInt, etc.).

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Value | Salida | Dato (Float) |

## Código generado

```python
__import__('random').random()
```

## Uso típico

### Condicionar por probabilidad

```
[On Update] → [BT Condition: {Random} < 0.3]
                  └── True ──► [...]  # ocurre el 30% de los frames
```

## Notas

- Devuelve un valor diferente cada frame — no lo uses para comparaciones entre frames sin almacenar el resultado.
- Para un rango personalizado, usa [Random Float](../math/random-float.md). Para la semilla, usa [Set Random Seed](../math/set-random-seed.md).
- Marcado como legacy porque [Random Float](../math/random-float.md) ofrece la misma funcionalidad con más opciones.
