# HUD State Publisher

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

Agrega y republica estado de gameplay bajo el prefijo `ui_*`, creando una capa de abstracción segura para nodos de HUD y UI. Nunca expone prefijos internos (`_ph_*`, `_inv_*`, `_pi_*`, etc.) a los consumidores de interfaz.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Update Rate | Float (0–10) | `0.0` | Segundos entre publicaciones; `0.0` = cada frame |
| Health | Bool | `True` | Publica `ui_hp` y `ui_hp_max` |
| Ammo | Bool | `True` | Publica `ui_ammo`, `ui_magazines`, `ui_weapon_name` |
| Reload | Bool | `True` | Publica `ui_reload` y `ui_reload_progress` |
| ADS | Bool | `True` | Publica `ui_ads` y `ui_ads_alpha` |
| Interaction | Bool | `True` | Publica `ui_interaction` y `ui_interaction_text` |
| Debug | Bool | `False` | Imprime los valores `ui_*` en cada ciclo de publicación |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables publicadas

### Salud

| Variable | Tipo | Fuente | Descripción |
|----------|------|--------|-------------|
| `ui_hp` | float | `player_hp` | Salud actual |
| `ui_hp_max` | float | `player_hp_max` | Salud máxima |

### Munición

| Variable | Tipo | Fuente | Descripción |
|----------|------|--------|-------------|
| `ui_ammo` | int | `weapon_ammo_in_mag` | Balas en el cargador |
| `ui_magazines` | int | `weapon_magazines` | Cargadores adicionales |
| `ui_weapon_name` | string | `weapon_type` | Nombre del arma activa |

### Recarga

| Variable | Tipo | Fuente | Descripción |
|----------|------|--------|-------------|
| `ui_reload` | bool | `weapon_is_reloading` | `True` mientras se recarga |
| `ui_reload_progress` | float (0–1) | `weapon_reload_progress` | Progreso de la recarga |

### ADS

| Variable | Tipo | Fuente | Descripción |
|----------|------|--------|-------------|
| `ui_ads` | bool | `weapon_ads_active` | `True` mientras se apunta |
| `ui_ads_alpha` | float (0–1) | `weapon_ads_alpha` | Alfa de transición ADS |

### Interacción

| Variable | Tipo | Fuente | Descripción |
|----------|------|--------|-------------|
| `ui_interaction` | bool | `interaction_available` | `True` cuando hay objeto interactuable en rango |
| `ui_interaction_text` | string | `interaction_type` | Tipo de interacción disponible |

## Throttling (Update Rate)

Cuando `Update Rate > 0`, el nodo solo escribe las variables `ui_*` cada N segundos. Esto reduce el trabajo de los nodos de HUD que no necesitan actualización cada frame.

- `_ui_dirty` es `True` durante el frame de publicación — úsalo en nodos UI para saber cuándo redibujar.
- Un cambio de arma (`weapon_type`) fuerza `_ui_dirty = True` dentro del ciclo actual, incluso si no ha expirado el intervalo.
- Las secciones desactivadas (p. ej. `Ammo = False`) se eliminan completamente del código compilado.

## Posición en el grafo

```
... → [Interaction System] → [HUD State Publisher] → [UI nodes]
```

Debe ir **después** de todos los nodos de gameplay y **antes** de cualquier consumidor de HUD.

## Notas de diseño

Este nodo existe para desacoplar el HUD del pipeline de gameplay. Los nodos de UI leen `ui_*` en lugar de acceder directamente a `player_hp`, `weapon_ammo_in_mag`, etc., lo que permite cambiar la implementación interna sin afectar los consumidores de interfaz.
