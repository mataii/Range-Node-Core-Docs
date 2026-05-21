# Save Game

**Type:** Action (branch)  
**Category:** SAVE

Persists the entire contents of `Range.logic.globalDict` to a file on disk. Branches based on whether the save succeeded or failed.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Filename | String | `"savegame"` | Base file name (without extension) |
| Extension | String | `"dat"` | File extension |
| Slot | Int | `0` | Numeric suffix for multiple slots (0 = no suffix) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Saved | Output | Exec |
| Error | Output | Exec |

## Generated code

```python
# Slot=0 → no suffix
try:
    Range.logic.saveGlobalDict('savegame', 'dat')
    # Saved branch
except:
    # Error branch

# Slot=1 → suffix "_1"
try:
    Range.logic.saveGlobalDict('savegame_1', 'dat')
    # Saved branch
except:
    # Error branch
```

## Typical usage

### Save on F5 press

```
[On Key Press: Key=F5, Mode=PRESSED]
    → [Save Game: Filename="savegame", Extension="dat", Slot=0]
          └── Saved ──► [Set Text: Text="Saved"]
          └── Error ──► [Set Text: Text="Save failed"]
```

### Multiple save slots

```
[On Message: Subject="save_slot_1"]
    → [Save Game: Filename="save", Slot=1]
          └── Saved ──► [...]
          └── Error ──► [...]
```

## Notes

- Saves **all** of `globalDict`, not just specific keys.
- The file is created at the project path (`//`).
- Before calling this node, make sure all data has been written via [Set Save Data](set-save-data.md) or [Property To Save](copy-property-to-save.md).
