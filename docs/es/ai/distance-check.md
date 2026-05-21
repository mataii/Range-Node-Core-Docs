# Distance Check

**Tipo:** Branch (Near / Far)  
**Categoría:** AI

Mide la distancia entre el objeto propietario y un objetivo de escena, y bifurca la ejecución. Además expone la distancia calculada en la variable local `_ai_dist` para que otros nodos puedan leerla.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Target | Enum | *(objetos de escena)* | Objeto de referencia |
| Threshold | Float (≥0.01) | `5.0` | Distancia límite para separar Near/Far |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Near | Salida | Exec (distancia ≤ threshold) |
| Far | Salida | Exec (distancia > threshold) |
| Distance | Salida | Data (float) |

## Comportamiento

Cada frame:

1. Busca el objeto Target en la escena.
2. Si existe: calcula distancia 3D a `worldPosition`.
3. Si no existe: distancia = 9999.
4. Si `distancia ≤ threshold` → ejecuta `Near`.
5. Si `distancia > threshold` → ejecuta `Far`.

La variable local `_ai_dist` queda disponible en el código generado para los nodos que se ejecuten después.

## Uso típico

### Bifurcación de comportamiento por distancia

```
[Distance Check: Player, Threshold=10.0]
    ├── Near ──► [Seek: Player]
    └── Far  ──► [Patrol]
```

### Zona de ataque

```
[Distance Check: Player, Threshold=2.0]
    ├── Near ──► [BT Custom Task: attack]
    └── Far  ──► [Seek: Player]
```

### Encadenado con Line Of Sight

```
[Distance Check: Player, Threshold=15.0]
    ├── Near ──► [Line Of Sight: Player]
    │               ├── Visible ──► [Seek: Player]
    │               └── Hidden  ──► [Wander]
    └── Far  ──► [Patrol]
```

## Notas

- La distancia se calcula en 3D (incluye diferencia de altura). Si quieres solo XY, usa un BTCustomTask manual.
- El socket `Distance` es de tipo Data — conectarlo a otro nodo de dato que lo lea. No es un valor publicado en la escena.
- Si el Target no está en escena, el nodo siempre elige `Far` (distancia = 9999).
- Distance Check no actualiza ninguna variable `ai_*`. Para el sistema modular, usa **Enemy Perception** que publica `ai_dist`.
