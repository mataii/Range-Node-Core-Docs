# Clear Save Data

**Type:** Action  
**Category:** SAVE

Erases all contents of `Range.logic.globalDict`. Useful for resetting save state or cleaning up data from a previous session.

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
Range.logic.globalDict.clear()
```

## Typical usage

### Wipe data before starting a new game

```
[On Message: Subject="new_game"]
    → [Clear Save Data]
    → [Set Save Data: Key="health",  Value=100]
    → [Set Save Data: Key="score",   Value=0]
    → [Save Game: Filename="savegame"]
```

### Reset after deleting save from menu

```
[On Message: Subject="delete_save"]
    → [Clear Save Data]
    → [Save Game: Filename="savegame"]  # overwrites with empty dict
```

## Notes

- Clears **all** in-memory keys. If [Save Game](save-game.md) is called afterward, the file will be empty.
- To remove only a specific key, use [Delete Save Data](delete-save-data.md).
- This action cannot be undone once executed.
