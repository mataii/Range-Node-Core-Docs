# Set Text

**Type:** Action  
**Category:** UI

Sets the text of a font object (`KX_FontObject`) in the scene. Accepts a dynamic data value or static text.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Object | Enum | — | Text object in the scene |
| Text | String | `""` | Static text (used when the Text socket is not connected) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Text | Input | Data (String) |
| Out | Output | Exec |

## Generated code

```python
_txt_obj = self.own.scene.objects.get('score_text')
if _txt_obj and hasattr(_txt_obj, 'text'):
    _txt_obj.text = str(42)
```

## Typical usage

### Speedometer

```
[On Update]
    → [Set Text:
           Object=speed_label
           Text={Number To String:
                     Value={Get Velocity: Component=Speed XY}
                     Decimals=1
                     Suffix=" km/h"}]
```

### Score display

```
[On Message: Subject="score_update"]
    → [Set Text:
           Object=score_label
           Text={Concat String:
                     A="Score: "
                     B={Get Property: prop="score"}}]
```

### Dynamic dialogue text

```
[On Update]
    → [Set Text: Object=dialogue_box, Text={Get Dialogue Text}]
```

## Notes

- The object must be a **Text** (font) object in Range Game Engine — not a Mesh object.
- The value is automatically converted to `str()`, so any data type can be connected.
- The object can be in the main scene or in a HUD overlay scene.
