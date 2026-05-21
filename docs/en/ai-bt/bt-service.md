# BT Service

**Type:** Exec (In → Out)  
**Category:** AI_BT  
**Result:** always `RUNNING`

Executes Python code at regular intervals (not every frame). Useful for periodic update tasks that would be expensive every frame: searching for enemies, recalculating paths, polling data into the blackboard.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Interval (s) | Float (≥0.016) | `0.5` | Seconds between code executions |
| Code | String | `"self._bt_bb['service_tick'] = True"` | Python executed every Interval seconds |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Behavior

Every frame:
1. Accumulates `deltaTime()` into an internal timer (`_btsvc_<sid>`).
2. If timer ≥ Interval: executes `Code` and resets timer to 0.
3. Always sets `_bt_last_result = 'RUNNING'`.
4. Continues through `Out`.

The code **does not execute on the first frame** — the timer must accumulate to Interval first.

## Variables available in `Code`

Same as BTCustomTask: `self`, `self.own`, `self._bt_bb`, `Range`, `mathutils`.

## Difference from BT Simple Parallel and BT Custom Task

| Node | Frequency | Result |
|------|-----------|--------|
| BT Simple Parallel | Every frame | `RUNNING` |
| BT Service | Every N seconds | `RUNNING` |
| BT Custom Task | Every frame | Configurable |

## Typical usage

### Search for player every 0.5 seconds

```
Interval: 0.5
Code:
  player = self.own.scene.objects.get('Player')
  if player:
      diff = player.worldPosition - self.own.worldPosition
      self._bt_bb['enemy_dist']    = diff.length
      self._bt_bb['enemy_visible'] = diff.length < 20.0
  else:
      self._bt_bb['enemy_visible'] = False
```

### Update patrol waypoint every 2 seconds

```
Interval: 2.0
Code:
  idx = (self._bt_bb.get('waypoint_idx', 0) + 1) % 4
  self._bt_bb['waypoint_idx'] = idx
  self._bt_bb['waypoint'] = 'Waypoint_' + str(idx)
```

### Detect obstacles with a periodic raycast

```
Interval: 0.25
Code:
  fwd = self.own.worldOrientation.col[1]
  rc  = self.own.rayCast(self.own.worldPosition + fwd * 5.0, None, 5.0)
  self._bt_bb['obstacle_ahead'] = rc[0] is not None
```

## Graph position

Place it at the start of the tree so data is updated before conditions read it:

```
[OnUpdate] → [BT Service: update perception data]
                    │
                    Out ──► [BT Condition: enemy_visible] → ...
```

## Notes

- Minimum interval is `0.016s` (~1 frame at 60fps). For per-frame intervals, use **BTSimpleParallel** or **BTCustomTask**.
- Errors in `Code` are printed to console but don't interrupt the tree — the node returns `RUNNING` regardless.
- The timer is per node instance. Multiple BTService nodes in the same tree have independent timers.
- Since it always returns `RUNNING`, don't place it as the sole node before a Selector — the Selector will always go to Failure. Use the `Out` socket to continue the tree.
