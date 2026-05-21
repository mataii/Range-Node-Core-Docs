# HUD State Publisher

**Type:** Exec (In → Out)  
**Category:** PLAYER

Aggregates and republishes gameplay state under the `ui_*` prefix, creating a safe abstraction layer for HUD and UI nodes. Never exposes internal prefixes (`_ph_*`, `_inv_*`, `_pi_*`, etc.) to interface consumers.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Update Rate | Float (0–10) | `0.0` | Seconds between publishes; `0.0` = every frame |
| Health | Bool | `True` | Publishes `ui_hp` and `ui_hp_max` |
| Ammo | Bool | `True` | Publishes `ui_ammo`, `ui_magazines`, `ui_weapon_name` |
| Reload | Bool | `True` | Publishes `ui_reload` and `ui_reload_progress` |
| ADS | Bool | `True` | Publishes `ui_ads` and `ui_ads_alpha` |
| Interaction | Bool | `True` | Publishes `ui_interaction` and `ui_interaction_text` |
| Debug | Bool | `False` | Prints `ui_*` values each publish cycle |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published variables

### Health

| Variable | Type | Source | Description |
|----------|------|--------|-------------|
| `ui_hp` | float | `player_hp` | Current health |
| `ui_hp_max` | float | `player_hp_max` | Maximum health |

### Ammo

| Variable | Type | Source | Description |
|----------|------|--------|-------------|
| `ui_ammo` | int | `weapon_ammo_in_mag` | Bullets in magazine |
| `ui_magazines` | int | `weapon_magazines` | Reserve magazines |
| `ui_weapon_name` | string | `weapon_type` | Active weapon name |

### Reload

| Variable | Type | Source | Description |
|----------|------|--------|-------------|
| `ui_reload` | bool | `weapon_is_reloading` | `True` while reloading |
| `ui_reload_progress` | float (0–1) | `weapon_reload_progress` | Reload progress |

### ADS

| Variable | Type | Source | Description |
|----------|------|--------|-------------|
| `ui_ads` | bool | `weapon_ads_active` | `True` while aiming |
| `ui_ads_alpha` | float (0–1) | `weapon_ads_alpha` | ADS transition alpha |

### Interaction

| Variable | Type | Source | Description |
|----------|------|--------|-------------|
| `ui_interaction` | bool | `interaction_available` | `True` when an interactable object is in range |
| `ui_interaction_text` | string | `interaction_type` | Available interaction type |

## Throttling (Update Rate)

When `Update Rate > 0`, the node only writes `ui_*` variables every N seconds. This reduces work for HUD nodes that don't need per-frame updates.

- `_ui_dirty` is `True` during the publish frame — use it in UI nodes to know when to redraw.
- A weapon change (`weapon_type`) forces `_ui_dirty = True` within the current cycle, even if the interval hasn't expired.
- Disabled sections (e.g. `Ammo = False`) are completely removed from compiled code.

## Graph position

```
... → [Interaction System] → [HUD State Publisher] → [UI nodes]
```

Must come **after** all gameplay nodes and **before** any HUD consumer.

## Design notes

This node exists to decouple the HUD from the gameplay pipeline. UI nodes read `ui_*` instead of accessing `player_hp`, `weapon_ammo_in_mag`, etc. directly, allowing internal implementation changes without affecting interface consumers.
