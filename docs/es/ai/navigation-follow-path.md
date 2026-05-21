# Navigation Follow Path

**Tipo:** Exec (In → Out)  
**Categoría:** AI  
**Execution Order:** 54 (después de Navigation Pathfinder, antes de Enemy Movement)

Sigue el path calculado por Navigation Pathfinder, avanzando waypoint a waypoint. Redirige `ai_move_target` al waypoint actual para que Enemy Movement se mueva en la dirección correcta. Detecta si el enemigo está bloqueado.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Waypoint Tolerance | Float (≥0.1) | `1.0` | Distancia XY para considerar un waypoint alcanzado |
| Stuck Timeout | Float (≥0.1) | `2.0` | Segundos sin moverse antes de resetear el path |
| Stuck Speed | Float (≥0.01) | `0.1` | Velocidad mínima (u/s) para no considerarse bloqueado |
| Debug | Bool | `False` | Imprimir eventos de waypoint y stuck por consola |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables leídas

| Variable | Fuente |
|----------|--------|
| `ai_nav_path` | Navigation Pathfinder |
| `ai_nav_index` | Navigation Pathfinder |
| `ai_state` | AI State Machine |

## Variables publicadas / modificadas

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `self.ai_reached_destination` | bool | True cuando el path se completó |
| `self.ai_move_target` | Vector | Redirigido al waypoint actual del path |
| `self.ai_nav_index` | int | Avanzado al llegar a cada waypoint |

## Comportamiento

### Por frame

1. Lee `ai_nav_path` y `ai_nav_index`.
2. Si el path está vacío o el estado es IDLE/PATROL/DEAD: resetea y no hace nada.
3. Si el índice ha pasado el final del path: `ai_reached_destination = True`.
4. Calcula distancia XY al waypoint actual del path.
5. Si `distancia ≤ waypoint_tolerance`: avanza `ai_nav_index`, redirige `ai_move_target` al siguiente.
6. Si todavía en ruta: mantiene `ai_move_target` apuntando al waypoint actual.

### Detección de bloqueo (stuck)

Cada frame compara la posición actual con la del frame anterior:
- Si la velocidad estimada < `stuck_speed` durante ≥ `stuck_timeout` segundos: resetea `ai_nav_index` a 0.
- Esto fuerza a Navigation Pathfinder a recalcular el path en el próximo ciclo.

La detección funciona independientemente del estado de navegación.

## Flujo de datos

```
NavigationPathfinder → ai_nav_path = [wp0, wp1, wp2, destino]
                        ai_nav_index = 0

NavigationFollowPath → ai_move_target = path[0]   (frame 1)
                     → ai_move_target = path[1]   (cuando llega a wp0)
                     → ai_move_target = destino   (cuando llega a wp2)
                     → ai_reached_destination = True

EnemyMovement → lee ai_move_target y se mueve hacia él
```

## Uso típico

```
[Navigation Pathfinder] → [Navigation Follow Path] → [Enemy Movement]
```

### Sin waypoints en escena

Si Navigation Pathfinder no encontró waypoints y el path es `[destino]`:
- Navigation Follow Path lo sigue como si fuera un único waypoint.
- Comportamiento equivalente a movimiento directo sin pathfinding.

## Notas

- La tolerancia de waypoint se compara en el **plano XY** — la diferencia de altura no importa para avanzar al siguiente punto.
- El reset del índice a 0 por stuck no borra el path — solo resetea la posición de recorrido. Navigation Pathfinder detectará que el índice cambió y recalculará si corresponde.
- `ai_reached_destination` es True solo cuando el último waypoint (el destino final) fue alcanzado.
- Si el path cambia entre frames (porque Pathfinder recalculó), el índice también se resetea a 0 automáticamente en el Pathfinder.
