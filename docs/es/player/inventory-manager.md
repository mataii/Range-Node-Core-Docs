# Inventory Manager

**Tipo:** Branch (In → Changed / Idle)  
**Categoría:** PLAYER

Gestiona hasta 10 slots de inventario de armas. Lee los eventos de **Player Input** (`_pi_fire`, `_pi_reload`, `_pi_slot_changed`) y los procesa para actualizar el estado de munición y el slot activo.

Ramifica a `Changed` cuando ocurre un evento (disparo, recarga, cambio de slot) o a `Idle` en caso contrario.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Slot Count | Int (1–10) | `10` | Número de slots activos |
| Slot [n] — Enable | Bool | `False` | Activa el slot n |
| Slot [n] — Type | String | `""` | Identificador de tipo de arma (ej: `PISTOL`, `RIFLE`) |
| Slot [n] — Melee | Bool | `False` | Arma cuerpo a cuerpo (sin munición) |
| Slot [n] — Ammo | Int | `30` | Balas iniciales en el cargador |
| Slot [n] — Mag Size | Int | `30` | Capacidad máxima del cargador |
| Slot [n] — Magazines | Int | `5` | Cargadores de reserva |
| Slot [n] — Max Mags | Int | `5` | Máximo de cargadores de reserva |
| Debug | Bool | `False` | Imprime el estado del inventario en la consola en cada evento |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Changed | Salida | Exec | Cuando ocurre un evento |
| Idle | Salida | Exec | Cuando no hay eventos |

## Eventos y último evento (`_inv_last_evt`)

| Evento | Condición | Valor de `_inv_last_evt` |
|--------|-----------|--------------------------|
| Slot Changed | `_pi_slot_changed` | `"SLOT_CHANGE"` |
| Disparó | `_pi_fire` y hay balas | `"FIRED"` |
| Cargador vacío | `_pi_fire` y sin balas | `"EMPTY"` |
| Recargó | `_pi_reload` y hay cargadores | `"RELOADED"` |
| Sin munición | `_pi_reload` y sin cargadores | `"NO_AMMO"` |
| Sin evento | — | `"NONE"` |

## Estado publicado (`_inv_*`)

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `_inv_slots` | list[dict] | Lista de diccionarios con el estado de cada slot |
| `_inv_active` | int | Índice del slot activo (0–9) |
| `_inv_last_evt` | string | Último evento ocurrido este frame |
| `_inv_changed` | bool | `True` si hubo algún evento este frame |

Cada dict en `_inv_slots` contiene: `exists`, `type`, `melee`, `ammo_in_mag`, `ammo_max`, `magazines`, `mag_max`.

## Integración con guardado

En el primer frame intenta restaurar munición y tipo de arma activa desde las propiedades BGE `_sl_inventory` y `_sl_weapon_state` del objeto.

## Grafo de ejemplo

```
[Player Input] → [Weapon Fire Executor] → [Inventory Manager]
                                               ├── Changed ──► [Weapon State Publisher] → ...
                                               └── Idle    ──► (continúa)
```

!!! warning "Requiere Player Input en el mismo objeto"
    Lee `_pi_fire`, `_pi_reload` y `_pi_slot_changed` directamente.
