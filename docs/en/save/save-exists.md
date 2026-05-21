# Save Exists

**Type:** Action (branch)  
**Category:** SAVE

Checks whether a save file exists on disk and branches execution accordingly.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Filename | String | `"savegame"` | Base file name |
| Extension | String | `"dat"` | File extension |
| Slot | Int | `0` | Numeric slot suffix to check |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Exists | Output | Exec |
| Not Found | Output | Exec |

## Generated code

```python
import os as _os
_save_path = _os.path.join(Range.logic.expandPath('//'), 'savegame.dat')
if _os.path.exists(_save_path):
    # Exists branch
else:
    # Not Found branch
```

## Typical usage

### Decide whether to load or start fresh

```
[On Start]
    → [Save Exists: Filename="savegame"]
          └── Exists    ──► [Load Game: Filename="savegame"]
          └── Not Found ──► [...]  # initialise default values
```

### Show "Continue" button only if save exists

```
[On Start]
    → [Save Exists: Filename="savegame"]
          └── Exists    ──► [Set Visibility: Object="btn_continue", Visible=True]
          └── Not Found ──► [Set Visibility: Object="btn_continue", Visible=False]
```

## Notes

- Checks the file on disk, not in memory. Use [Has Save Data](has-save-data.md) to check in-memory keys.
- The path is built relative to the project directory (`//`).
