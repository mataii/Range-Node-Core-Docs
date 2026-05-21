# Property From Save

**Type:** Action  
**Category:** SAVE

Copies a value from `Range.logic.globalDict` into a BGE property on the object. Equivalent to [Get Save Data](get-save-data.md) followed by [Set Property](../data/set-property.md).

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Save Key | String | `"player_health"` | Key to read from globalDict |
| Property | Enum | — | BGE property on the object to write the value into |
| Default | String | `"0"` | Value used if the key does not exist in globalDict |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
self.own['health'] = Range.logic.globalDict.get('player_health', 0)
```

## Typical usage

### Restore properties after loading

```
[On Start]
    → [Load Game: Filename="savegame"]
          └── Loaded ──► [Property From Save: Save Key="health",  Property=health,  Default=100]
                      ──► [Property From Save: Save Key="score",   Property=score,   Default=0]
                      ──► [Property From Save: Save Key="current_level", Property=level, Default=1]
```

## Notes

- If the key does not exist in globalDict, the `Default` value is written to the property.
- If the BGE property does not exist on the object, it is created automatically with the type of the value.
- Natural pair of [Property To Save](copy-property-to-save.md).
