# Navigation Pathfinder

**Tipo:** Exec (In → Out)  
**Categoría:** AI  
**Execution Order:** 52 (después de AI State Machine, antes de Navigation Follow Path)

Calcula un camino (path) mediante BFS sobre un grafo de waypoints de escena. El resultado se almacena en `ai_nav_path` para que Navigation Follow Path lo siga.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Waypoint Prop | String | `"waypoint"` | Nombre de la propiedad BGE que identifica los waypoints |
| Connect Radius | Float (≥0.5) | `6.0` | Distancia máxima para conectar dos waypoints como vecinos |
| Repath Interval | Float (≥0.05) | `0.5` | Segundos entre recálculos del path |
| Move Threshold | Float (≥0.1) | `1.0` | El destino debe moverse esta distancia para forzar un recálculo anticipado |
| Debug | Bool | `False` | Imprimir info de path por consola |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables publicadas

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `self.ai_nav_path` | list[Vector] | Lista de posiciones del camino |
| `self.ai_nav_index` | int | Índice del waypoint actual (inicializado a 0) |
| `self.ai_nav_repath_timer` | float | Segundos desde el último recálculo |

## Variables leídas

| Variable | Fuente |
|----------|--------|
| `ai_move_target` | AI State Machine |
| `ai_target_pos` | Enemy Perception (fallback) |
| `ai_state` | AI State Machine |

## Comportamiento

### Activación

El pathfinder solo opera cuando:
- `ai_state` es `CHASE` o `SEARCH`
- Existe un `ai_move_target` válido (Vector)

En otros estados (IDLE, PATROL, DEAD), limpia el path y lo deja vacío.

### Construcción del grafo

1. Recolecta todos los objetos de escena con la propiedad BGE `waypoint_prop`.
2. Conecta pares de waypoints cuya distancia ≤ `connect_radius`.
3. Resultado: grafo de proximidad no dirigido.

> Los waypoints deben tener la propiedad BGE añadida desde el editor de propiedades de Range. Por ejemplo: propiedad `waypoint = True` (tipo Boolean).

### BFS

1. Encuentra el waypoint más cercano a la posición propia → nodo inicio.
2. Encuentra el waypoint más cercano al destino → nodo fin.
3. BFS desde inicio hasta fin → lista de nombres de waypoints.
4. Convierte nombres a posiciones Vector → `ai_nav_path`.
5. Añade la posición exacta del destino al final del path.

### Fallback sin waypoints

Si no hay waypoints en escena, el path es simplemente `[destino]` — movimiento en línea recta.

### Condiciones de recálculo

El path se recalcula cuando:
- El timer de cooldown (`_nav_repath_cd`) llega a 0 (cada `repath_interval` segundos)
- El destino se ha desplazado más de `move_threshold` unidades respecto al último cálculo

## Setup de waypoints en escena

1. Crea Empty objects o cualquier objeto en la escena.
2. Añade la propiedad BGE (ej. `waypoint`) a cada uno.
3. Colócalos en la zona de patrulla con espacio ≤ `connect_radius` entre vecinos.
4. El pathfinder los detecta automáticamente en runtime.

```
WP_A ──── WP_B ──── WP_C
  └─────────────────┘
  (si distancia ≤ connect_radius, se conectan)
```

## Uso típico

```
[AI State Machine] → [Navigation Pathfinder] → [Navigation Follow Path] → [Enemy Movement]
```

## Notas

- El grafo se reconstruye en cada recálculo, no se cachea entre frames (los waypoints pueden moverse).
- Si dos waypoints están dentro del `connect_radius` pero hay un muro entre ellos, el pathfinder los conectará igualmente — no hace raycast entre waypoints. Para mapas con muros complejos, ajusta `connect_radius` o coloca waypoints estratégicamente.
- El path incluye siempre la posición exacta del destino al final, por lo que el enemigo llega al punto exacto aunque el waypoint más cercano no esté muy cerca.
- `ai_nav_index` se resetea a 0 en cada nuevo recálculo. Navigation Follow Path lo incrementa conforme avanza.
