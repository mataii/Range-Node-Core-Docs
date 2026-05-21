# Load Game

**Type:** Action (branch)  
**Category:** SAVE

Loads a save file and replaces the contents of `Range.logic.globalDict` with the file's data. Branches based on whether the load succeeded or failed.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Filename | String | `"savegame"` | Base file name (without extension) |
| Extension | String | `"dat"` | File extension |
| Slot | Int | `0` | Numeric slot suffix to load (0 = no suffix) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Loaded | Output | Exec |
| Error | Output | Exec |

## Generated code

```python
try:
    Range.logic.loadGlobalDict('savegame', 'dat')
    # Loaded branch
except:
    # Error branch
```

## Typical usage

### Load on game start

```
[On Start]
    → [Save Exists: Filename="savegame"]
          └── Exists    ──► [Load Game: Filename="savegame"]
                                └── Loaded ──► [Property From Save: Save Key="health", Property=health]
                                └── Error  ──► [Set Text: Text="Load failed"]
          └── Not Found ──► [...]  # first session
```

## Notes

- `Load Game` **overwrites** all of `globalDict` with the file contents.
- If you need to merge data without overwriting, load before writing new data.
- Check file existence with [Save Exists](save-exists.md) before loading to avoid the Error branch.
