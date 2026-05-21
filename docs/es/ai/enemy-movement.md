# Enemy Movement

**Tipo:** Exec (In → Out)  
**Categoría:** AI  
**Execution Order:** 55 (después de Navigation Follow Path)

Mueve al objeto propietario hacia `ai_move_target` con velocidad suavizada. Publica `ai_is_moving` para que Enemy Animation sepa si reproducir Walk o Idle.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Speed | Float (≥0.01) | `3.0` | Velocidad de movimiento (unidades/s) |
| Stop Radius | Float (≥0.0) | `1.0` | Distancia al objetivo en la que se detiene |
| Vel Lerp | Float (0.01–1.0) | `0.2` | Suavizado de velocidad (1=instantáneo) |
| Physics | Enum | `Character` | Modo de física |

### Modos de física

| Modo | Método |
|------|--------|
| `Character` | `applyMovement()` × `deltaTime()` |
| `Dynamic` | Asigna `localLinearVelocity.x/y` (preserva Z) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables leídas

| Variable | Fuente | Descripción |
|----------|--------|-------------|
| `ai_move_target` | Navigation Follow Path / AI State Machine | Posición de destino |
| `ai_target_pos` | Enemy Perception | Fallback si no hay nav target |

## Variables publicadas

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `self.ai_is_moving` | bool | True si velocidad > 0.1 |

## Comportamiento

Prioridad de destino:
1. `ai_move_target` (Navigation Follow Path lo redirige al waypoint actual)
2. `ai_target_pos` (posición directa del target, si no hay nav)

Si el destino es un Vector válido:
- Calcula dirección XY hacia el destino.
- Si `distancia > stop_radius`: interpola velocidad hacia la dirección deseada.
- Si `distancia ≤ stop_radius`: frena la velocidad a cero.

Si no hay destino válido: frena completamente.

## Uso típico

### Movimiento básico hacia target

```
[Enemy Perception] → [AI State Machine] → [Enemy Movement]
```

### Con navegación por waypoints

```
[AI State Machine] → [Navigation Pathfinder] → [Navigation Follow Path] → [Enemy Movement]
```

Navigation Follow Path redirige `ai_move_target` al waypoint actual, Enemy Movement lo sigue.

### Ajuste de velocidades por estado

Enemy Movement tiene una velocidad única. Para velocidades distintas por estado (patrulla lenta, persecución rápida), usa dos nodos Enemy Movement en ramas paralelas:

```
[AI State Machine]
    │
[Enemy Decision]
    ├── Chase ──► [Enemy Movement: Speed=5.0]
    └── Idle  ──► [Enemy Movement: Speed=2.0]
```

## Notas

- El eje Z se ignora en la dirección de movimiento.
- `ai_is_moving` es consumido por Enemy Animation para decidir entre Walk e Idle.
- `_emv_vel` es el estado interno de velocidad suavizada. Si hay dos Enemy Movement en el mismo componente, comparten esta variable — renombrar los nodos para crear variables separadas.
- Si `ai_move_target` y `ai_target_pos` son ambos None, el nodo frena sin error.
