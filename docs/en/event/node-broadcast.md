# Node Broadcast

**Type:** Exec (In → Out)  
**Category:** EVENT

Sender of the RNC internal message bus. Sends a message with a `subject` and optional data to all [On Message](on-message.md) nodes listening on that same subject.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Subject | String | `"my_event"` | Message identifier to send |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Data | Input | Data (any type) |
| Out | Output | Exec |

## Generated code

```python
Range.logic.globalDict['rnc_msg_<subject>']      = True
Range.logic.globalDict['rnc_msg_<subject>_data'] = <data>
```

The message is a boolean flag in `globalDict`. Receivers (`On Message`) read and consume it in the same frame.

## Data socket

Accepts any Python expression. If not connected, the default value is `None`. Receivers read it as `_msg_data`.

Valid types for Data:
- Literals: `42`, `"hello"`, `True`, `[1, 2, 3]`
- Expressions: `self._bt_bb.get('score', 0)`, `self.own.worldPosition.copy()`
- `None` (no data)

## Typical usage

### Simple event with no data

```
[BT Condition: hp <= 0]
    └── True ──► [Node Broadcast: subject="player_died"]
```

### Event with attached data

```
# Send the player's position on death
[Node Broadcast: subject="player_died",
                 Data = self.own.worldPosition.copy()]
```

### Notification pipeline

```
[On Update] → [Enemy Combat]
                    → [BT Condition: ai_attack_requested]
                              └── True ──► [Node Broadcast: subject="enemy_attacked"]
```

### Multiple receivers for the same subject

```
# Manager object:
[On Update] → [On Message: "enemy_attacked"] → [update counter]

# HUD object:
[On Update] → [On Message: "enemy_attacked"] → [show indicator]

# Audio object:
[On Update] → [On Message: "enemy_attacked"] → [play sound]
```

All objects with `On Message: "enemy_attacked"` will receive the broadcast.

## When to use vs alternatives

| Situation | Recommended solution |
|-----------|---------------------|
| Notify a specific object | `globalDict['_dmg_' + obj.name]` (direct channel) |
| Notify all without knowing how many | **Node Broadcast** |
| Communication within the same object | Direct `self.*` variables |
| Complex data between scenes | Manual `globalDict` |

## Notes

- Node Broadcast writes the flag **every frame it executes** — if connected directly to On Update without a condition, it will send the message on every frame.
- The flag persists in `globalDict` until `On Message` consumes it. If no object has an active receiver, the flag stays `True` until something reads or clears it.
- To clear a message manually: `Range.logic.globalDict.pop('rnc_msg_<subject>', None)`.
- Node Broadcast executes sequentially like any Exec node — the message will be available in `globalDict` in the **same frame**, so receivers in other objects that execute after it can read it.
