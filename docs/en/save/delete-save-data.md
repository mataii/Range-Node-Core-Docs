# Delete Save Data

**Type:** Action  
**Category:** SAVE

Removes a key from `Range.logic.globalDict`. Has no effect if the key does not exist.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Key | String | `"player_health"` | Key to remove from globalDict |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
if 'player_health' in Range.logic.globalDict:
    del Range.logic.globalDict['player_health']
```

## Typical usage

### Clear temporary data on level exit

```
[On Message: Subject="level_end"]
    → [Delete Save Data: Key="temp_flag"]
```

### Invalidate session on death

```
[On Message: Subject="game_over"]
    → [Delete Save Data: Key="current_run_score"]
```

## Notes

- Deletion is in-memory only. Call [Save Game](save-game.md) afterward to persist the change to disk.
- To remove all keys at once, use [Clear Save Data](clear-save-data.md).
