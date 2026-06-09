# BT Move To

**Tipo:** Exec (In → Out)  
**Categoría:** AI_BT  
**Tarea**

Mueve el objeto propietario hacia un objetivo de la escena. Devuelve `RUNNING` mientras se desplaza, `SUCCESS` al llegar dentro de la distancia de llegada, y `FAILURE` si el objetivo no existe en la escena.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Target | Enum | *(objetos de escena)* | Nombre del objeto destino |
| Speed | Float (≥0.01) | `3.0` | Velocidad de movimiento (unidades/s) |
| Arrive Dist | Float (≥0.05) | `1.0` | Distancia para considerar que llegó al objetivo |
| Vel Lerp | Float (0.01–1.0) | `0.2` | Suavizado de velocidad (1 = instantáneo, menor = más suave) |
| Physics | Enum | `Character` | Modo de física del objeto |

### Modos de física

| Modo | Método de movimiento |
|------|---------------------|
| `Character` | `applyMovement()` en espacio mundo |
| `Dynamic` | Asigna `localLinearVelocity.x/y` (preserva Z) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Resultados

| Condición | `_bt_last_result` |
|-----------|-------------------|
| Objetivo no existe en la escena | `'FAILURE'` |
| Distancia al objetivo ≤ Arrive Dist | `'SUCCESS'` |
| En tránsito | `'RUNNING'` |

## Movimiento interno

El nodo mantiene una velocidad interna (`_btmv_<sid>_vel`) que hace lerp hacia la dirección deseada cada frame:

```
dirección = (pos_objetivo - pos_propia).normalized() * Speed
velocidad_actual = lerp(velocidad_actual, dirección, Vel Lerp)
```

El eje Z se ignora en el cálculo de dirección — el movimiento es siempre horizontal.

Al llegar o al recibir `FAILURE`, la velocidad se frena a cero.

## Uso típico

### Seguir al jugador

```
Target: Player
Speed: 4.0
Arrive Dist: 1.5
```

```
[BT Condition: enemy_visible]
    ├── True ──► [BT Move To: Player] → [BT Selector]
    │                                        ├── Success ──► [BT Custom Task: attack]
    │                                        └── Failure ──► [BT Wait: 0.3s]
    └── False ──► [BT Move To: patrol_point]
```

### Patrulla entre waypoints

Combinado con BTSetBlackboard para cambiar el punto destino dinámicamente:

```
[BT Move To: Waypoint_A] → [BT Selector]
                                ├── Success ──► [BT Set Blackboard: mode="go_B"]
                                └── Failure ──► [BT Wait: 0.5s]
```

## Notas

- El `Target` se selecciona en tiempo de **edición** desde los objetos de la escena activa. Si el objeto no existe en runtime, devuelve `FAILURE`.
- Para targets dinámicos (el jugador en otra escena, un objeto instanciado en runtime), usa **BTCustomTask** con `applyMovement` directo y la posición del blackboard.
- `Vel Lerp = 1.0` da movimiento inmediato (sin inercia). `Vel Lerp = 0.05` da una aceleración muy gradual.
- El estado de velocidad (`_btmv_<sid>_vel`) usa el nombre del nodo — si tienes dos BTMoveTo en el mismo árbol, cada uno tiene su propia velocidad interna.
