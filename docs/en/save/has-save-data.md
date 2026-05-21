# Has Save Data

**Type:** Action (branch)  
**Category:** SAVE

Checks whether a key exists in `Range.logic.globalDict` and branches execution accordingly.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Key | String | `"player_health"` | Key to look up in globalDict |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Found | Output | Exec |
| Not Found | Output | Exec |

## Generated code

```python
if 'player_health' in Range.logic.globalDict:
    # Found branch
else:
    # Not Found branch
```

## Typical usage

### Detect existing save on game start

```
[On Start]
    → [Has Save Data: Key="save_version"]
          └── Found     ──► [Load Game: Filename="savegame"]
          └── Not Found ──► [Set Save Data: Key="save_version", Value=1]
```

### Conditional read

```
[On Update]
    → [Has Save Data: Key="high_score"]
          └── Found     ──► [Set Text: Text={Get Save Data: Key="high_score"}]
          └── Not Found ──► [Set Text: Text="---"]
```

## Notes

- Only checks whether the key exists in memory (`globalDict`), not in a disk file.
- To check whether the save file exists on disk, use [Save Exists](save-exists.md).
