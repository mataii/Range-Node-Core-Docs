# Property To Save

**Type:** Action  
**Category:** SAVE

Copies the value of a BGE property from the object into `Range.logic.globalDict`. Equivalent to [Set Save Data](set-save-data.md) with the property's value as input.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Property | Enum | — | BGE property on the object to copy |
| Save Key | String | `""` | Key in globalDict (empty = same name as the property) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
Range.logic.globalDict['health'] = self.own.get('health', None)
```

## Typical usage

### Save multiple properties before writing to disk

```
[On Key Press: Key=F5, Mode=PRESSED]
    → [Property To Save: Property=health]
    → [Property To Save: Property=score]
    → [Property To Save: Property=level,  Save Key="current_level"]
    → [Save Game: Filename="savegame"]
```

## Notes

- If `Save Key` is empty, the globalDict key is the property name itself.
- If the property does not exist on the object, stores `None`.
- To load back, use [Property From Save](copy-property-from-save.md).
