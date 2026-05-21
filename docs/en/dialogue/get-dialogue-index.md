# Dialogue Index

**Type:** Data  
**Category:** DIALOGUE

Returns the zero-based index of the current dialogue line. Useful for triggering events at specific lines or showing a progress indicator.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Dialogue ID | String | `"dlg1"` | Must match the Dialogue Player |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Index | Output | Data (Int) |

## Generated code

```python
getattr(self, '_dlg_idx_dlg1', 0)
```

## Typical usage

### Trigger an event on the second line (index 1)

```
[On Update] → [Dialogue Player: ID="intro"]
                  └── Line Changed ──► [BT Condition: {Dialogue Index: ID="intro"} == 1]
                                           └── True ──► [Play Action: Action="npc_gesture"]
```

## Notes

- Index starts at `0` for the first line.
- Returns `0` if the dialogue is not active.
