# Start Dialogue

**Type:** Action  
**Category:** DIALOGUE

Starts a dialogue sequence by writing a signal to `globalDict`. The [Dialogue Player](dialogue-player.md) with the same ID will pick it up on the next frame.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Dialogue ID | String | `"dlg1"` | Must match the corresponding Dialogue Player |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
Range.logic.globalDict['_dlg_start_dlg1'] = True
```

## Typical usage

### Start on NPC collision

```
[On Collision: Object="npc_old_man"]
    → [Start Dialogue: Dialogue ID="intro"]
    → [Overlay Scene: Scene=DialogueHUD, Mode=Add]
```

### Start on E key near an object

```
[On Key Press: Key=E, Mode=PRESSED]
    → [Start Dialogue: Dialogue ID="chest_dialogue"]
```

## Notes

- This node only writes a flag to `globalDict` — it does not play anything by itself.
- The [Dialogue Player](dialogue-player.md) with the same ID controls actual playback.
- If the dialogue was already active when this node is called, it restarts from the first line.
