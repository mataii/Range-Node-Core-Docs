# Get Subtitle

**Type:** Data  
**Category:** DIALOGUE

Reads the text of the active subtitle with the given ID. Returns the text while the subtitle has not expired; returns an empty string otherwise.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Subtitle ID | String | `"sub1"` | Must match the corresponding Show Subtitle |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Text | Output | Data (String) |

## Generated code

```python
(lambda _s: _s['text'] if _s and Range.logic.getClockTime() < _s['until'] else '')
(Range.logic.globalDict.get('_sub_sub1'))
```

## Typical usage

```
[On Update]
    → [Set Text: Object=subtitle_obj, Text={Get Subtitle: ID="sub1"}]
```

With this pattern, the text object shows the subtitle while it is active and displays an empty string when it expires — automatically clearing the text with no additional logic.

## Notes

- Connecting to [Set Text](../ui/set-text.md) is the standard pattern.
- Text is automatically cleared on expiry (no extra node needed).
- Natural pair of [Show Subtitle](show-subtitle.md).
