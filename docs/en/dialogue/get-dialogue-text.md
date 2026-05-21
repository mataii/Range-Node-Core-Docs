# Dialogue Text

**Type:** Data  
**Category:** DIALOGUE

Returns the text of the current dialogue line. Must be on the same object as the [Dialogue Player](dialogue-player.md) with the same ID.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Dialogue ID | String | `"dlg1"` | Must match the Dialogue Player |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Text | Output | Data (String) |

## Generated code

```python
getattr(self, '_dlg_text_dlg1', '')
```

## Typical usage

```
[On Update] → [Dialogue Player: ID="intro"]
                  └── Line Changed ──► [Set Text: Object=text_box, Text={Dialogue Text: ID="intro"}]
```

## Notes

- Returns an empty string if the dialogue is not active or has not started.
- Only updates when `Dialogue Player` changes lines — no need to read it in the `Idle` branch.
