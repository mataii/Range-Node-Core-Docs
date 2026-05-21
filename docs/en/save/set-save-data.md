# Set Save Data

**Type:** Action  
**Category:** SAVE

Writes a value to `Range.logic.globalDict` under the given key. Data stays in memory until [Save Game](save-game.md) is called to persist it to disk.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Key | String | `"player_health"` | Key in globalDict |
| Default Value | String | `"0"` | Value used if the Value socket is not connected |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Value | Input | Data (any type) |
| Out | Output | Exec |

## Generated code

```python
Range.logic.globalDict['player_health'] = 100
```

## Typical usage

### Save score

```
[On Update]
    → [Set Save Data: Key="score", Value={Get Property: prop="score"}]
```

### Save player position

```
[On Message: Subject="checkpoint"]
    → [Set Save Data: Key="checkpoint_x", Value={Object Position: Axis=X}]
    → [Set Save Data: Key="checkpoint_y", Value={Object Position: Axis=Y}]
    → [Set Save Data: Key="checkpoint_z", Value={Object Position: Axis=Z}]
```

## Notes

- Data only exists in memory until [Save Game](save-game.md) is called.
- If the key already exists, its value is overwritten.
- Shortcut alternative: [Property To Save](copy-property-to-save.md) copies an entire BGE property in one step.
