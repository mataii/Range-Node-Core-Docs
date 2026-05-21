# On Message

**Type:** Branch (Received / None)  
**Category:** EVENT

Receiver of the RNC internal message bus. Waits for a message with a specific `subject` sent by [Node Broadcast](node-broadcast.md). When received, executes the `Received` path and exposes the attached data.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Subject | String | `"my_event"` | Message identifier to listen for |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Received | Output | Exec (message received this frame) |
| None | Output | Exec (no message) |
| Data | Output | Data (any type — payload from Node Broadcast) |

## Generated code

```python
# Key in globalDict: "rnc_msg_<subject>"
if Range.logic.globalDict.get('rnc_msg_my_event', False):
    _msg_data = Range.logic.globalDict.get('rnc_msg_my_event_data', None)
    Range.logic.globalDict['rnc_msg_my_event'] = False  # consume
    # → Received
else:
    # → None
```

The message is **consumed** on read: the flag is set to `False` so the same message does not fire across multiple frames.

## globalDict keys used

| Key | Content |
|-----|---------|
| `rnc_msg_<subject>` | Bool — True when a message is pending |
| `rnc_msg_<subject>_data` | Any value sent by Node Broadcast |

## Typical usage

### Receive a death event

```
# Object A (enemy) with Node Broadcast:
[On Update] → [BT Condition: ai_is_dead]
                  └── True ──► [Node Broadcast: subject="enemy_died"]

# Object B (wave manager) with On Message:
[On Update] → [On Message: subject="enemy_died"]
                  └── Received ──► [BTCustomTask: wave_manager.enemy_count -= 1]
```

### Receive data with the message

```
# Sender:
[Node Broadcast: subject="player_scored", Data = self._bt_bb.get('score_delta', 0)]

# Receiver:
[On Update] → [On Message: subject="player_scored"]
                  └── Received ──► [BTCustomTask:
                                       total = Range.logic.globalDict.get('total_score', 0)
                                       Range.logic.globalDict['total_score'] = total + _msg_data]
```

### Cross-scene communication

Node Broadcast / On Message use `globalDict`, which persists between scenes (if not cleared):

```
# Scene A sends:
[Node Broadcast: subject="cutscene_done"]

# Scene B receives (if it loads before the flag expires):
[On Start] → [On Message: subject="cutscene_done"]
```

## Difference from Range's native sendMessage()

| On Message / Node Broadcast | Native sendMessage() |
|-----------------------------|---------------------|
| RNC internal bus via globalDict | Logic Bricks system |
| Works between Python components | Works between sensors/actuators |
| Attached data (any type) | Subject and body (strings only) |
| Not compatible with Message Sensor | Not compatible with On Message |

## Notes

- If multiple objects have On Message with the same `subject`, the flag is global — the first object to read it this frame consumes it, and the rest receive `False`. For targeted messages, include the recipient's name in the subject: `"msg_Enemy_01"`.
- The flag is consumed in the same frame it is received. If On Message does not execute that frame (object out of range, tree not running), the message is lost.
- `_msg_data` is the local variable available in nodes following the Received path.
- To inspect or manually clear a pending message: `Range.logic.globalDict.pop('rnc_msg_<subject>', None)`.
