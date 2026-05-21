# Enemy Decision

**Type:** Branch (Chase / Idle)  
**Category:** AI  
**Execution Order:** 51 (after AI State Machine)

A branching node that reads `ai_can_see` and splits execution into two paths: chase if the target is visible, idle if not.

## Properties

None — the node reads `ai_can_see` directly from `self`.

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Chase | Output | Exec (target visible) |
| Idle | Output | Exec (target not visible) |

## Behavior

```python
if getattr(self, 'ai_can_see', False):
    # → Chase
else:
    # → Idle
```

Uses `getattr` to be safe if Enemy Perception is not in the scene.

## Typical usage

Enemy Decision is the central routing node in the modular system. It is placed **after** AI State Machine to branch movement and combat nodes:

### Basic pipeline with Enemy Decision

```
[Enemy Perception] → [Damage Receiver] → [AI State Machine]
                                               │ Out
                                       [Enemy Decision]
                                           ├── Chase ──► [Enemy Movement] → [Enemy Combat]
                                           └── Idle  ──► [Wander]
```

### With per-branch animations

```
[Enemy Decision]
    ├── Chase ──► [Enemy Movement]
    │                  │
    │             [Enemy Animation: Walk]
    └── Idle  ──► [Enemy Animation: Idle]
```

## Difference from AI State Machine

| Enemy Decision | AI State Machine |
|---------------|-----------------|
| Simple branch (visible/not visible) | Full state machine (7 states) |
| No configuration | 7 adjustable properties |
| For simple pipelines | For complex behaviors |

## Alternatives

For logic more complex than visible/not-visible, read `ai_state` directly in a BTCustomTask:

```python
state = getattr(self, 'ai_state', 'IDLE')
if state == 'ATTACK':
    self._bt_last_result = 'SUCCESS'
elif state == 'CHASE':
    self._bt_last_result = 'RUNNING'
else:
    self._bt_last_result = 'FAILURE'
```

## Notes

- Chase/Idle are suggested names — the node actually reflects `ai_can_see`, not the SM state.
- If Enemy Perception is not in the tree, `ai_can_see` defaults to False → always executes Idle.
- To react to the full SM state (CHASE, ATTACK, RETREAT...), use a BTCondition that checks `self.ai_state == 'CHASE'` instead of Enemy Decision.
