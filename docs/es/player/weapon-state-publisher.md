# Weapon State Publisher

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

Puente normalizador entre **Inventory Manager** y el resto del pipeline de armas. Lee el slot activo de `_inv_slots` y publica todas las variables `weapon_*` en un formato estándar que los demás nodos consumen.

Debe colocarse **después** de Inventory Manager en el grafo.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables publicadas (`weapon_*`)

Estas variables son **de solo lectura** para todos los nodos excepto este.

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `weapon_exists` | bool | El slot activo tiene un arma |
| `weapon_type` | string | Tipo del arma activa (ej: `"PISTOL"`) |
| `weapon_is_melee` | bool | El arma activa es cuerpo a cuerpo |
| `weapon_ammo` | int | Balas en el cargador actual |
| `weapon_ammo_max` | int | Capacidad máxima del cargador |
| `weapon_magazines` | int | Cargadores de reserva |
| `weapon_magazines_max` | int | Máximo de cargadores de reserva |
| `weapon_slot` | int | Índice del slot activo |
| `weapon_can_fire` | bool | `True` si existe el arma Y (es melee O tiene balas) |
| `weapon_fire_requested` | bool | `True` el frame en que `_inv_last_evt == "FIRED"` |
| `weapon_reload_requested` | bool | `True` el frame en que `_inv_last_evt == "RELOADED"` |

## Posición en el grafo

```
[Inventory Manager]
    ├── Changed ──► [Weapon State Publisher] → [Weapon Fire Executor] → ...
    └── Idle    ──►
```

!!! note "Por qué existe este nodo"
    Inventory Manager trabaja con dicts Python (`_inv_slots[n]`). Este nodo convierte esos dicts a variables planas tipadas (`weapon_ammo: int`), haciendo que el resto del pipeline sea independiente de la implementación interna del inventario.
