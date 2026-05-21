# BT Simple Parallel

**Type:** Exec (In → Out)  
**Category:** AI_BT  
**Result:** always `RUNNING`

Executes a **background action** every frame (writing a value to the blackboard) while the main execution continues through `Out`. Simulates parallelism: a task that runs continuously while the tree processes other branches.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| BB Key | String | `""` | Blackboard key to update every frame |
| BB Value | String | `"True"` | Python expression evaluated every frame for the value |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Behavior

Every frame the node executes:

1. Evaluates `BB Value` as a Python expression.
2. Writes the result to `self._bt_bb[BB Key]`.
3. Sets `_bt_last_result = 'RUNNING'`.
4. Continues through `Out`.

If `BB Key` is empty, step 2 is skipped (`pass`).

## Use cases

### Continuously update target position

```
BB Key:   target_pos
BB Value: self.own.scene.objects.get('Player').worldPosition.copy() if self.own.scene.objects.get('Player') else None
```

The blackboard will always have the player's updated position, available for BTCondition or BTMoveTo in the same frame.

### Mark active state

```
BB Key:   is_chasing
BB Value: True
```

While this node is in the active tree, `is_chasing` stays `True`.

### Calculate distance to target

```
BB Key:   dist_to_player
BB Value: (self.own.worldPosition - self.own.scene.objects.get('Player', self.own).worldPosition).length
```

## Graph position

```
[OnUpdate] → [BT Simple Parallel: update target_pos]
                    │
                    Out ──► [BT Condition: enemy_visible] → ...
```

Place it at the start of the tree so background data is fresh before evaluating conditions.

## Notes

- Always returns `RUNNING` — cannot `SUCCESS` or `FAILURE`. If you need a conditional result, use **BTCustomTask** or **BTService**.
- Unlike **BTService**, **BT Simple Parallel** executes its code **every frame**, not at intervals.
- Errors in `BB Value` don't crash the tree — the field is silently skipped if empty.
