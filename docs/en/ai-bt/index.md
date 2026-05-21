# AI_BT Category — Behavior Trees

The **AI_BT** category implements a **Behavior Tree** system for defining AI agent decision-making logic. Instead of imperative code, behavior is described as a graph of nodes with three possible results: `SUCCESS`, `FAILURE`, and `RUNNING`.

## Core concepts

### The three result states

Each BT node writes to `self._bt_last_result` when it finishes executing:

| State | Meaning |
|-------|---------|
| `SUCCESS` | The task completed successfully |
| `FAILURE` | The task could not be completed |
| `RUNNING` | The task is in progress (continues next frame) |

This value is read by composite nodes (Selector, Sequence) that come immediately after in the graph.

### The Blackboard

The **blackboard** is a Python dictionary shared by all BT nodes on the same object:

```python
self._bt_bb  # dict{} — persists between frames
```

It is used to communicate data between nodes without connecting sockets: enemy position, whether a target is visible, patrol state, etc.

```python
# Write (BTSetBlackboard or BTCustomTask):
self._bt_bb['enemy_visible'] = True
self._bt_bb['target_pos']    = enemy.worldPosition.copy()

# Read (BTCondition):
val = self._bt_bb.get('enemy_visible', False)
```

### Execution model in RNC

Unlike classic BTs that traverse the entire tree in one frame, **RNC executes the graph linearly every frame** from `OnUpdate`. Each node:

1. Executes its logic.
2. Writes `_bt_last_result`.
3. The next node in the graph reads it and reacts.

This means **RUNNING results propagate to the next frame automatically** — the graph re-executes from the start every frame and nodes with internal state (BTWait, BTMoveTo) detect they are already active and update their progress.

## Node groups

### Composites
Composites read `_bt_last_result` and branch execution:

| Node | Type | Logic |
|------|------|-------|
| [BT Selector](bt-selector.md) | Branch | OR — `SUCCESS` → success branch, else → failure branch |
| [BT Sequence](bt-sequence.md) | Branch | AND — `!= FAILURE` → continue, `FAILURE` → abort |
| [BT Simple Parallel](bt-simple-parallel.md) | Exec | Runs a background action every frame, always `RUNNING` |

### Decorators
Decorators modify the result of the previous node:

| Node | Type | Effect |
|------|------|--------|
| [BT Inverter](bt-inverter.md) | Exec | Flips `SUCCESS` ↔ `FAILURE` |
| [BT Repeater](bt-repeater.md) | Exec | Accumulates N successes before propagating `SUCCESS` |
| [BT Condition](bt-condition.md) | Branch | Reads blackboard, evaluates condition, branches |

### Tasks
Tasks perform work and report their result:

| Node | Type | Result |
|------|------|--------|
| [BT Wait](bt-wait.md) | Exec | `RUNNING` while waiting, `SUCCESS` when done |
| [BT Move To](bt-move-to.md) | Exec | `RUNNING` in transit, `SUCCESS` on arrival, `FAILURE` with no target |
| [BT Set Blackboard](bt-set-blackboard.md) | Exec | Writes to blackboard, always `SUCCESS` |
| [BT Play Animation](bt-play-animation.md) | Exec | `RUNNING` while playing, `SUCCESS` when done |
| [BT Custom Task](bt-custom-task.md) | Exec | Free Python code — must set `_bt_last_result` |
| [BT Service](bt-service.md) | Exec | Runs code every N seconds, always `RUNNING` |

## Typical graph pattern

```
[OnUpdate]
    │
    ▼
[BT Condition: enemy_visible]
    ├── True ──► [BT Move To: enemy] ──► [BT Selector]
    │                                          ├── Success ──► [BT Play Animation: attack]
    │                                          └── Failure ──► [BT Wait: 0.5s]
    │
    └── False ──► [BT Set Blackboard: mode="patrol"] ──► [BT Move To: waypoint]
```

## Golden rule

> **A Task node must always precede the composite that reads its result.**  
> The order in the graph determines the execution order within the same frame.

```
✓ [BTWait] → [BTSelector]    ← Selector reads Wait's result
✗ [BTSelector] → [BTWait]    ← Selector reads _bt_last_result from a previous frame
```
