# Save Load System

**Type:** Exec (In → Out)  
**Category:** PLAYER

Saves and loads game state to JSON files. Supports multiple slots, configurable keys, timed autosave, and automatic load on startup. Can persist player health, weapon ammo, and world object properties.

## Properties

### File

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Path | String | `"//saves/"` | Save file directory (`//` = game folder) |
| Slot | Int (0–9) | `0` | Active save slot index |
| Version | Int (≥1) | `1` | Save format version; files with a different version are rejected |

### Keys

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Save Key | Enum | `T` | Key to trigger a manual save |
| Load Key | Enum | `B` | Key to trigger a manual load |

### Startup and autosave

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Load on Start | Bool | `False` | Loads the slot automatically on the first frame if it exists |
| Autosave | Bool | `False` | Enables timed automatic saving |
| Autosave Rate | Float (≥5) | `60.0` | Seconds between autosaves |

### Save content

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Player State | Bool | `True` | Includes `player_hp` and `player_hp_max` |
| Ammo State | Bool | `True` | Includes `weapon_ammo_in_mag`, `weapon_magazines`, `weapon_type` |
| World Objects | Bool | `False` | Saves `sl_*` properties of objects with the BGE property `_sl_id` |

### Debug

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Debug | Bool | `False` | Prints save/load events to the console |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published variables

| Variable | Type | Description |
|----------|------|-------------|
| `save_slot` | int | Active slot |
| `save_exists` | bool | `True` if a file exists in the current slot |
| `save_success` | bool | One-frame pulse after a successful save or load |
| `save_loading` | bool | `True` on the frame a load executes |

## File format

Files are saved as `slot_<N>.json` inside the directory configured in `Path`. Example with all options enabled:

```json
{
  "_version": 1,
  "player_hp": 75.0,
  "player_hp_max": 100.0,
  "weapon_ammo_in_mag": 12,
  "weapon_magazines": 3,
  "weapon_type": "rifle",
  "world": {
    "42": { "sl_open": true },
    "17": { "sl_collected": 1 }
  }
}
```

## World object saving

When `World Objects = True`, the node scans the scene for all objects with the BGE property `_sl_id` and saves all their properties that start with `sl_`. On load, it restores those properties to objects with the matching `_sl_id`.

```
Scene object:
  _sl_id = 42          (unique identifier)
  sl_open = False      (saved and restored)
  sl_collected = 0     (saved and restored)
```

## Integration with other nodes

SaveLoadSystem **does not write directly** to gameplay variables. It publishes data to BGE properties on the owner object (`self.own['_sl_*']`), and gameplay nodes (Player Health, Inventory Manager) read them on their initialization frame to restore their state:

| BGE property | Consumed by |
|--------------|-------------|
| `_sl_player_hp` | Player Health |
| `_sl_player_hp_max` | Player Health |
| `_sl_inventory` | Inventory Manager |
| `_sl_weapon_state` | Weapon State Publisher |
| `_sl_loaded` | (load completed flag) |

## Graph position

```
[OnUpdate] → [Save Load System] → [Player Health] → [Inventory Manager] → ...
```

Must come **before** the nodes that consume its restoration data.

## Notes

- `Version` acts as protection: if the saved file has a different version than the configured one, it is rejected and not loaded.
- `save_success` is a one-frame pulse — use it in a Branch node to show feedback to the player.
- Files are created with `os.makedirs(..., exist_ok=True)`, so the directory is created automatically if it doesn't exist.
