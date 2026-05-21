# FLOW Category

The **FLOW** category contains nodes that control *when* and *how many times* execution happens, independently of data values. They don't evaluate external data — they maintain their own internal state to modify the execution flow.

## Difference from conditions

| Type | Examples | Reads external data |
|------|----------|---------------------|
| Condition | BTCondition, Distance Check | Yes (`ai_dist`, blackboard…) |
| FLOW | Do Once, Flip Flop, Gate, Delay | No — only counts or measures time |

FLOW nodes are **data-blind** — it doesn't matter what arrives, only when and how many times.

## Nodes

| Node | Type | Description |
|------|------|-------------|
| [Do Once](do-once.md) | Branch (True/False) | Executes True only the first time; False on all subsequent calls |
| [Flip Flop](flip-flop.md) | Branch (True/False) | Alternates True/False on each call |
| [Gate](gate.md) | Branch (Out/Skip) | Lets execution through every N calls |
| [Delay](delay.md) | Branch (True/False) | Executes True after N seconds have elapsed |

## Internal variables per node

Each node uses instance variables named after the node:

| Node | Internal variable(s) |
|------|----------------------|
| Do Once | `self._do1_<name>` |
| Flip Flop | `self._ff_<name>` |
| Gate | `self._gate_<name>` |
| Delay | `self._dly_<name>`, `self._dly_<name>_done` |

Renaming a node in the editor resets its state — the node name determines the variables.

## Common patterns

### One-time initialization

```
[OnUpdate] → [Do Once]
                ├── True  ──► [BT Set Blackboard: initialized = True] → [setup logic]
                └── False ──► (nothing — already initialized)
```

### State toggle

```
[OnCollision] → [Flip Flop]
                    ├── True  ──► [BT Custom Task: open_door()]
                    └── False ──► [BT Custom Task: close_door()]
```

### Heartbeat every 5 seconds

```
[OnUpdate] → [Delay: 5.0s, Repeat=True]
                 ├── True  ──► [BTCustomTask: update_minimap()]
                 └── False ──► (waiting)
```

### Run only every 3 active frames

```
[OnUpdate] → [Gate: N=3]
                 ├── Out  ──► [expensive logic]
                 └── Skip ──► (nothing)
```
