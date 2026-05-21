# Inventory State

**Tipo:** Data (sin sockets exec)  
**Categoría:** PLAYER

Nodo de datos puro. Lee el estado del slot de inventario activo almacenado por **Inventory Manager** en el mismo objeto.

## Propiedades

| Propiedad | Tipo | Descripción |
|-----------|------|-------------|
| Data | Enum | Valor a exponer |

### Opciones del selector Data

| Opción | Tipo | Descripción |
|--------|------|-------------|
| Weapon Type | string | Tipo del arma activa (ej: `"PISTOL"`, `"NONE"`) |
| Ammo In Mag | int | Balas restantes en el cargador actual |
| Mag Size | int | Capacidad máxima del cargador |
| Magazines | int | Cargadores de reserva disponibles |
| Max Magazines | int | Máximo de cargadores de reserva |
| Active Slot | int | Índice del slot activo (0–9) |
| Last Event | string | Último evento: `NONE / FIRED / EMPTY / RELOADED / NO_AMMO / SLOT_CHANGE` |
| Exists | bool | `True` si el slot activo tiene un arma asignada |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Value | Salida | Data |

## Uso típico

```
[Inventory State: AMMO_IN_MAG] ──► [UI Text: Set]
[Inventory State: LAST_EVENT] ──► [Compare: == "FIRED"] ──► [Play Sound Gunshot]
[Inventory State: EXISTS] ──► [Branch] ──► [Show Crosshair]
```

!!! info "Requiere Inventory Manager en el mismo objeto"
    Sin Inventory Manager, devuelve valores por defecto (`"NONE"`, 0, `False`).
