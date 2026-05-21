# Is Dialogue Active

**Type:** Data  
**Category:** DIALOGUE

Returns `True` if the dialogue with the given ID is currently playing, `False` otherwise.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Dialogue ID | String | `"dlg1"` | Must match the Dialogue Player |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Active | Output | Data (Bool) |

## Generated code

```python
getattr(self, '_dlg_active_dlg1', False)
```

## Typical usage

### Block movement while dialogue is active

```
[On Update]
    → [BT Condition: {Is Dialogue Active: ID="intro"} == False]
          └── True ──► [Apply Force: ...]  # only move if no dialogue
```

### Toggle dialogue HUD visibility

```
[On Update]
    → [Set Visibility:
           Object=dialogue_hud
           Visible={Is Dialogue Active: ID="intro"}]
```

## Notes

- Must be on the same object as the [Dialogue Player](dialogue-player.md) with the same ID.
- Returns `False` before the dialogue has ever been started.
