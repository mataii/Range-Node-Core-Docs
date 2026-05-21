# Set Random Seed

**Tipo:** Acción  
**Categoría:** MATH

Fija la semilla del generador de números aleatorios de Python para obtener secuencias reproducibles.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Seed | Int | `42` | Semilla para el generador. Debe ser un entero |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |
| Seed | Entrada | Dato (Int) |

## Código generado

```python
__import__('random').seed(42)
```

## Uso típico

### Generación procedural reproducible

```
[On Start] → [Set Random Seed: Seed={Get Property: prop="world_seed"}]
           → [BTCustomTask:
                  # generar mapa con random.randint / random.choice...
                  # produce siempre el mismo resultado con la misma semilla]
```

### Debug: reproducir un escenario específico

```
[On Start] → [Set Random Seed: Seed=12345]
           → [...]
```

## Notas

- Afecta al módulo `random` de Python globalmente — todos los nodos aleatorios que usen `random.*` se verán afectados.
- Llamar `Set Random Seed` en medio de una secuencia reinicia el generador, lo que puede romper la reproducibilidad si hay más llamadas aleatorias entre medias.
- Para semillas basadas en tiempo real (comportamiento no reproducible), usa `[BTCustomTask: import random; random.seed(int(Range.logic.getClockTime() * 1000))]`.
