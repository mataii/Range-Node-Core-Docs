# Player State

**Tipo:** Data (sin sockets exec)  
**Categoría:** PLAYER

Nodo de datos puro. Deriva el estado de movimiento del jugador a partir de los flags `_pi_*` (Player Input) y `_pj_*` (Player Jump). Útil para conectar a animaciones, UI o lógica de juego.

## Propiedades

| Propiedad | Tipo | Descripción |
|-----------|------|-------------|
| Data | Enum | Valor a exponer (ver tabla abajo) |

### Opciones del selector Data

| Opción | Tipo | Descripción |
|--------|------|-------------|
| State | string | `"IDLE"` / `"WALKING"` / `"JUMPING"` / `"FALLING"` |
| Is Idle | bool | `True` cuando está quieto y en el suelo |
| Is Walking | bool | `True` cuando se mueve y está en el suelo |
| Is Jumping | bool | `True` mientras el timer de salto está activo (ascenso) |
| Is Falling | bool | `True` cuando está en el aire pero **no** ascendiendo |
| Is Grounded | bool | `True` cuando está en el suelo |
| Is Airborne | bool | `True` cuando está en el aire |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Value | Salida | Data |

## Lógica interna

| Estado | Condición |
|--------|-----------|
| JUMPING | `_pj_timer > 0` |
| FALLING | `_pj_in_air AND NOT _pj_timer > 0` |
| WALKING | `_pi_is_moving AND NOT _pj_in_air` |
| IDLE | cualquier otro caso |

!!! warning "Requiere Player Input + Player Jump en el mismo objeto"
    Sin Player Jump, `_pj_in_air` y `_pj_timer` no existen y todos los estados aéreos devolverán `False`.
