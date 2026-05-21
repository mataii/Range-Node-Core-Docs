# Dialogue Speaker

**Type:** Data  
**Category:** DIALOGUE

Returns the speaker name for the current dialogue line. Must be on the same object as the [Dialogue Player](dialogue-player.md) with the same ID.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Dialogue ID | String | `"dlg1"` | Must match the Dialogue Player |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Speaker | Output | Data (String) |

## Generated code

```python
getattr(self, '_dlg_spkr_dlg1', '')
```

## Typical usage

```
[On Update] → [Dialogue Player: ID="intro"]
                  └── Line Changed ──► [Set Text: Object=speaker_label, Text={Dialogue Speaker: ID="intro"}]
```

## Notes

- Returns an empty string if the line's speaker field is empty or the dialogue is not active.
- Use this node to display the speaker name on a separate text object from the dialogue text.
