# Do Once

**Type:** Branch (True / False)  
**Category:** FLOW

Executes the `True` path only the very first time it is triggered. On all subsequent calls it executes the `False` path. Useful for one-time initializations or entry effects that must not repeat.

## Properties

None — the node only maintains an internal flag.

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| True | Output | Exec (first call) |
| False | Output | Exec (subsequent calls) |

## Behavior

```python
if not hasattr(self, '_do1_<name>'):
    self._do1_<name> = False
if not self._do1_<name>:
    self._do1_<name> = True
    # → True path
else:
    # → False path
```

- **First call:** the flag doesn't exist → created as False → set to True → executes `True`.
- **All subsequent calls:** the flag is already True → executes `False`.

The state persists as long as the object exists in the scene.

## Typical usage

### Blackboard initialization

```
[OnUpdate] → [Do Once]
                ├── True  ──► [BT Set Blackboard: patrol_idx = 0]
                │                   → [BT Set Blackboard: initialized = True]
                └── False ──► [normal logic]
```

### Entry sound when entering a zone

```
[OnCollision: zone_trigger] → [Do Once]
                                  ├── True  ──► [BTCustomTask: play_sound('alert')]
                                  └── False ──► (silence)
```

### Loot spawn on death (only once)

```
[BT Condition: ai_is_dead]
    └── True ──► [Do Once]
                    ├── True  ──► [BTCustomTask: self.own.scene.addObject('Coin', self.own, 0)]
                    └── False ──► (already dropped)
```

## Resetting the state

To reset Do Once from code:

```python
# In a BTCustomTask:
var = '_do1_' + 'NodeName'.replace(' ', '_')
if hasattr(self, var):
    delattr(self, var)
```

Or directly:
```python
self._do1_NodeName = False
```

## Notes

- The node name in the editor determines the variable name. Renaming the node is equivalent to resetting its state.
- If two Do Once nodes in the same component share the same name, they will share the variable — always give them unique names.
- The `False` path fires from the second call onwards, not only when the node has already been triggered. Connecting to `False` is useful for continuing normal execution without re-doing the initialization.
