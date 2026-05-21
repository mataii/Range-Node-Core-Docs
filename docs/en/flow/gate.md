# Gate

**Type:** Branch (Out / Skip)  
**Category:** FLOW

Lets execution through once every N calls. All intermediate calls go to `Skip`. Useful for reducing the frequency of expensive operations or for fire rhythms.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Every N calls | Int (≥1) | `2` | Number of calls between each firing |

The `Count` data socket can override this property at runtime.

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Count | Input | Data (Int) |
| Out | Output | Exec (every N calls) |
| Skip | Output | Exec (all other calls) |

## Behavior

```python
if not hasattr(self, '_gate_<name>'):
    self._gate_<name> = 0
self._gate_<name> += 1
if self._gate_<name> >= N:
    self._gate_<name> = 0
    # → Out path
else:
    # → Skip path
```

The counter starts at 0 and increments on each call. When it reaches N, it fires `Out` and resets to 0.

### Firing table with N=3

| Call | Counter before | Action |
|------|----------------|--------|
| 1st | 0→1 | Skip |
| 2nd | 1→2 | Skip |
| 3rd | 2→3 → reset to 0 | **Out** |
| 4th | 0→1 | Skip |
| 5th | 1→2 | Skip |
| 6th | 2→3 → reset to 0 | **Out** |

The first firing happens on call number N, not N+1.

## Typical usage

### Expensive logic every 3 OnUpdate frames

```
[OnUpdate] → [Gate: N=3]
                 ├── Out  ──► [BTCustomTask: expensive_recalculation()]
                 └── Skip ──► (nothing)
```

### Check every 5 patrol steps

```
[Patrol] → [Gate: N=5]
                ├── Out  ──► [BTCustomTask: check_environment()]
                └── Skip ──► (continue)
```

### Dynamic frequency from socket

Connect the `Count` socket to a blackboard variable:

```
BT Set Blackboard: difficulty_n = 2    (easy)
BT Set Blackboard: difficulty_n = 5    (hard)
```

```
[Gate: Count = self._bt_bb.get('difficulty_n', 3)]
```

### Fire rate (e.g., one bullet every 4 OnUpdate ticks)

```
[OnUpdate] → [Gate: N=4]
                 ├── Out  ──► [BTCustomTask: spawn_bullet()]
                 └── Skip ──► (waiting)
```

## Notes

- The `Count` socket accepts Python expressions. If not connected, uses the `Every N calls` property.
- The first Out fires on call #N, not #1. If you need it to fire on the first call, use N=1.
- With N=1: Out on every call (Gate doesn't block anything — equivalent to a pass-through node).
- The counter persists while the object exists. It doesn't reset between scenes if the object persists.
- To reset the counter manually: `self._gate_NodeName = 0`.
