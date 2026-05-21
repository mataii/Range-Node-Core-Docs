# Overlay Scene

**Type:** Action  
**Category:** UI

Adds, removes, or toggles an overlay scene in Range Game Engine. Overlay scenes are rendered on top of the active scene — ideal for HUDs, pause menus, and loading screens.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Scene | Enum | — | Scene to manage as overlay |
| Mode | Enum | `Add` | `Add` / `Remove` / `Toggle` |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
# Mode = Add
_ov_running = any(s.name == 'HUD' for s in Range.logic.getSceneList())
if not _ov_running:
    Range.logic.addScene('HUD', 1)

# Mode = Remove
for _s in Range.logic.getSceneList():
    if _s.name == 'HUD':
        _s.end()
        break

# Mode = Toggle
_ov_running = any(s.name == 'HUD' for s in Range.logic.getSceneList())
if _ov_running:
    for _s in Range.logic.getSceneList():
        if _s.name == 'HUD':
            _s.end()
            break
else:
    Range.logic.addScene('HUD', 1)
```

## Typical usage

### Show HUD on start

```
[On Start] → [Overlay Scene: Scene=HUD, Mode=Add]
```

### Pause menu with Escape key

```
[On Key Press: Key=ESC, Mode=PRESSED]
    → [Overlay Scene: Scene=PauseMenu, Mode=Toggle]
```

### Hide HUD during cutscene

```
[On Message: Subject="cutscene_start"]
    → [Overlay Scene: Scene=HUD, Mode=Remove]

[On Message: Subject="cutscene_end"]
    → [Overlay Scene: Scene=HUD, Mode=Add]
```

## Notes

- `Add` mode does nothing if the scene is already active (prevents duplicates).
- `Toggle` mode automatically checks whether the scene is running before adding or removing.
- The overlay scene must be in the same `.blend` project to appear in the selector.
- Overlay scenes have their own object space but share `globalDict`.
