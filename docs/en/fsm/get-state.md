# Get State

**Type:** Data  
**Category:** FSM

Data node that returns the current state name of the FSM as a string. Used as a data source connected to input sockets of other nodes.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| FSM ID | String | `"fsm"` | Identifier of the FSM to query |
| Default | String | `"idle"` | Value returned before `SetState` is ever called |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| State | Output | Data (String) |

## Generated code

```python
self.own.get('fsm_fsm_state', 'idle')
```

This is a pure Python expression — it is inserted directly wherever the socket is connected.

## Typical usage

### Display state as BGE property (debugging)

```
[On Update] → [BTCustomTask:
                   self.own['debug_state'] = {Get State: FSM ID="fsm"}]
```

### Pass state to another Set State

```
[Get State: FSM ID="source_fsm"] ──(State)──► [Set State: FSM ID="target_fsm"]
```

Copies the state from one FSM to another (useful for synchronizing objects).

### Condition on current state

```
[On Update] → [BT Condition: {Get State: FSM ID="door"} == "open"]
                  └── True ──► [allow_passage()]
```

### Store state in blackboard

```
[On Update] → [BT Set Blackboard: last_state = {Get State: FSM ID="ai"}]
```

### Check against a list of states

```
[BT Condition: {Get State: FSM ID="fsm"} in ['idle', 'patrol']]
```

## Notes

- `Get State` reads `self.own.get('fsm_<id>_state', default)` — if the property does not exist yet (before the first `SetState` call), it returns the configured `Default` value.
- As a pure Data node, it has no Exec In or Out sockets — it only connects to Data input sockets on other nodes.
- The returned value is always a `str` (BGE string properties).
- To log transitions, use `On Property Change` on `self.own['fsm_<id>_state']` directly — `Get State` is not needed for that.
