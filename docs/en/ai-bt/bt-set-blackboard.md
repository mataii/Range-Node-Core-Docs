# BT Set Blackboard

**Type:** Exec (In → Out)  
**Category:** AI_BT  
**Result:** always `SUCCESS`

Writes a value to the blackboard (`self._bt_bb`). Always returns `SUCCESS`. It is the primary mechanism for passing data between BT nodes without connecting sockets.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Key | String | `"my_key"` | Blackboard key to write |
| Value | String | `"True"` | Python expression evaluated at execution time |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Value expressions

The `Value` field is evaluated as Python code in the component context. `self` is available.

| Expression | Result |
|------------|--------|
| `True` | boolean True |
| `42` | integer 42 |
| `'patrol'` | string "patrol" |
| `self.own.worldPosition.copy()` | current object position |
| `self.own.scene.objects.get('Enemy').worldPosition` | another object's position |
| `self.own.get('hp', 100)` | BGE property of the object |

If the expression throws an exception, the key is set to `None` (no crash).

## Results

| Condition | `_bt_last_result` |
|-----------|-------------------|
| Always | `'SUCCESS'` |

## Typical usage

### Mark AI state

```
[BT Set Blackboard: mode = 'chase'] → [BT Move To: Player]
```

### Capture target position

```
[BT Custom Task: find_player] → [BT Set Blackboard: target_pos = self.own.scene.objects.get('Player').worldPosition.copy()]
```

### Reset cooldown

```
[BT Set Blackboard: attack_cooldown = 0.0] → [BT Sequence] → ...
```

### Increment a counter

```
Key:   patrol_index
Value: (self._bt_bb.get('patrol_index', 0) + 1) % 4
```

Creates a cyclic counter from 0 to 3 without external code.

## Graph position

BTSetBlackboard typically precedes the action that will use the written value, or is placed in condition branches to update state when a decision is made:

```
[BT Condition: enemy_visible]
    ├── True ──► [BT Set Blackboard: last_seen_pos = self.own.scene.objects.get('Player').worldPosition.copy()]
    │                   │
    │                   Out ──► [BT Move To: Player]
    └── False ──► [BT Move To: last_seen_pos_object]
```

## Notes

- Always returns `SUCCESS`, making it safe in any position within a Sequence.
- The blackboard persists between frames — values written are available the next frame.
- To read the blackboard, use **BTCondition** or access directly with `self._bt_bb.get('key', default)` in a **BTCustomTask**.
