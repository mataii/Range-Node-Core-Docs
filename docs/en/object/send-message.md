# Send Message

**Type:** Action  
**Category:** OBJECT

Sends a BGE message using Range Game Engine's native messaging system (`sendMessage`). Different from the RNC message bus (`Node Broadcast`).

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Subject | String | `"msg"` | Message subject |
| Body | String | `""` | Message body |
| To | String | `""` | Recipient object name. Empty = broadcast to all |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
self.own.sendMessage('event', '', '')  # broadcast
self.own.sendMessage('event', 'data', 'target_obj')  # to specific target
```

## Difference from Node Broadcast

| Send Message (native BGE) | Node Broadcast (RNC) |
|---------------------------|----------------------|
| Uses the native BGE system | Uses `globalDict` |
| Compatible with BGE `Message` sensors | Only works with RNC nodes |
| Can target specific objects | Global broadcast to all RNC nodes |
| Not available in all Range versions | Always available in RNC |

## Notes

- Prefer [Node Broadcast](../event/node-broadcast.md) for communication between RNC nodes.
- Use `Send Message` when you need interoperability with native BGE sensors/actuators.
