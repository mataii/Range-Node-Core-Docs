# BT Custom Task

**Type:** Exec (In → Out)  
**Category:** AI_BT  
**Task**

Executes free Python code. Must set `self._bt_last_result` to `'SUCCESS'`, `'FAILURE'`, or `'RUNNING'` based on the logic's outcome. It is the escape hatch of the BT system for any behavior not covered by the standard nodes.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Code | String | `"self._bt_last_result = 'SUCCESS'"` | Python code to execute every frame |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Variables available in `Code`

| Variable | Description |
|----------|-------------|
| `self` | The Python component (`KX_PythonComponent`) |
| `self.own` | The scene object (`KX_GameObject`) |
| `self._bt_bb` | The blackboard (persistent dict) |
| `self._bt_last_result` | The current result (read or write it) |
| `Range` | Range Game Engine logic module |
| `mathutils` | Vector math module |

## Error handling

If the code throws an exception:

1. The error is printed to console: `[BTCustomTask] Error: <exception>`.
2. `_bt_last_result = 'FAILURE'` is set automatically.
3. Execution continues through `Out` — no crash.

## Usage examples

### Attack the player with globalDict damage

```python
player = self.own.scene.objects.get('Player')
if player:
    dist = (player.worldPosition - self.own.worldPosition).length
    if dist < 1.5:
        key = '_dmg_' + player.name
        Range.logic.globalDict[key] = Range.logic.globalDict.get(key, 0) + 25.0
        self._bt_last_result = 'SUCCESS'
    else:
        self._bt_last_result = 'FAILURE'
else:
    self._bt_last_result = 'FAILURE'
```

### Detect player and store in blackboard

```python
player = self.own.scene.objects.get('Player')
if player:
    diff = player.worldPosition - self.own.worldPosition
    diff.z = 0.0
    if diff.length < 15.0:
        self._bt_bb['target'] = player
        self._bt_bb['dist']   = diff.length
        self._bt_last_result  = 'SUCCESS'
    else:
        self._bt_last_result  = 'FAILURE'
else:
    self._bt_last_result = 'FAILURE'
```

### Manual timer with persistent state

```python
if not hasattr(self, '_my_timer'):
    self._my_timer = 0.0
self._my_timer += Range.logic.deltaTime()
if self._my_timer >= 3.0:
    self._my_timer = 0.0
    self._bt_last_result = 'SUCCESS'
else:
    self._bt_last_result = 'RUNNING'
```

### End the object

```python
self.own.endObject()
self._bt_last_result = 'SUCCESS'
```

## Notes

- The `Code` field shows a single visible line in the editor, but can contain newlines (`\n`) if edited from Python or pasted with indentation.
- Do not use `exec()` inside BTCustomTask — the code is already being compiled and executed by the RNC compiler.
- If the logic is complex, consider creating an external Python module and importing it: `import my_ai_utils; my_ai_utils.attack(self)`.
- Always set `_bt_last_result` — if the code ends without doing so, the value from the previous frame persists and may cause unpredictable behavior.
