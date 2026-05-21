# Get Save Data

**Type:** Data  
**Category:** SAVE

Reads a value from `Range.logic.globalDict` by key. If the key does not exist, returns the configured default value.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Key | String | `"player_health"` | Key in globalDict |
| Default | String | `"0"` | Value returned when the key does not exist |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Value | Output | Data (type of stored value) |

## Generated code

```python
Range.logic.globalDict.get('player_health', 0)
```

## Typical usage

### Display saved health in HUD

```
[On Start]
    → [Load Game: Filename="savegame"]
          └── Loaded ──► [Set Text: Text={Get Save Data: Key="health", Default=100}]
```

### Restore player position

```
[On Start]
    → [Transform:
           X={Get Save Data: Key="checkpoint_x", Default=0}
           Y={Get Save Data: Key="checkpoint_y", Default=0}
           Z={Get Save Data: Key="checkpoint_z", Default=1}]
```

## Notes

- If the key does not exist (e.g. first session with no save file), returns the `Default` value.
- The return type depends on what was stored; use it with the correct type on the destination socket.
- To read a key and copy it directly into a BGE property, use [Property From Save](copy-property-from-save.md).
