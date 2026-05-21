# Show Subtitle

**Type:** Action  
**Category:** DIALOGUE

Displays a timed text (subtitle) for a given duration. The text is stored in `globalDict` with its expiry time; [Get Subtitle](get-subtitle.md) reads it and returns it while it has not expired.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Subtitle ID | String | `"sub1"` | Shared identifier with Get Subtitle |
| Text | String | `""` | Text to display (used when the Text socket is not connected) |
| Duration (s) | Float | `3.0` | Duration in seconds before the text expires |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Text | Input | Data (String) |
| Duration | Input | Data (Float) |
| Out | Output | Exec |

## Generated code

```python
Range.logic.globalDict['_sub_sub1'] = {
    'text': str("Watch out!"),
    'until': Range.logic.getClockTime() + float(3.0),
}
```

## Typical usage

### Tutorial hint

```
[On Start]
    → [Show Subtitle: ID="hint", Text="Press SPACE to jump", Duration=5.0]

[On Update]
    → [Set Text: Object=subtitle_obj, Text={Get Subtitle: ID="hint"}]
```

### Dynamic cutscene subtitle

```
[On Message: Subject="scene_event"]
    → [Show Subtitle: ID="narrator", Text={Get Save Data: Key="narrator_line"}, Duration=4.0]
```

## Notes

- Does nothing by itself — requires a [Get Subtitle](get-subtitle.md) connected to [Set Text](../ui/set-text.md) in `On Update`.
- Calling it again before expiry resets both the text and the timer.
- For manual-advance dialogue text, use [Dialogue Player](dialogue-player.md) instead.
