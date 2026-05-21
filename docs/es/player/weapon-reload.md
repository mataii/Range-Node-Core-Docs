# Weapon Reload

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

Añade una recarga con duración real sobre los eventos de **Inventory Manager**. Bloquea el disparo durante la recarga publicando `weapon_is_reloading = True`.

Debe colocarse **antes** de Weapon Fire Executor para que el bloqueo sea visible.

## Propiedades

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| Reload Time | Float | `2.0` | Duración de la recarga en segundos |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables publicadas

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `weapon_is_reloading` | bool | `True` durante toda la recarga |
| `weapon_reload_progress` | float (0.0–1.0) | Progreso de la recarga (útil para HUD/animación) |
| `weapon_reload_finished` | bool | Pulso de un frame al completarse la recarga |

## Flujo

```
frame
  │
  ├── Si _wr_active:
  │       timer -= dt
  │       Si timer <= 0:
  │           is_reloading = False
  │           reload_finished = True (un frame)
  │           progress = 1.0
  │       Si no:
  │           is_reloading = True
  │           progress = 1 - (timer / reload_time)
  │
  └── Si weapon_reload_requested y no _wr_active:
          _wr_active = True
          timer = reload_time
          is_reloading = True
```

## Posición en el grafo

```
[Weapon State Publisher] → [Weapon Reload] → [Weapon Fire Executor] → ...
                                ↓
                      [Weapon Animation]  ← lee weapon_is_reloading, weapon_reload_finished
```

!!! info "weapon_reload_requested"
    Este flag es publicado por Weapon State Publisher cuando `_inv_last_evt == "RELOADED"`. Inventory Manager ya validó que hay cargadores disponibles antes de publicarlo.
