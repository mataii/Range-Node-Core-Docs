# Toggle

**Type:** Action  
**Category:** LOGIC

Toggles the boolean value of a BGE property (`self.own[prop] = not self.own[prop]`).

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Property | String | `"active"` | BGE property name to toggle |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
self.own['active'] = not self.own['active']
```

## Typical usage

### Toggle flashlight mode

```
[On Key Press: Key=F, Mode=PRESSED] → [Toggle: prop="flashlight_on"]
```

### Toggle game pause

```
[On Key Press: Key=ESC, Mode=PRESSED] → [Toggle: prop="paused"]
```

### Visually toggle debug collisions

```
[On Key Press: Key=TAB, Mode=PRESSED]
    → [Toggle: prop="debug_visible"]
    → [BTCustomTask:
           self.own.setVisible(self.own.get('debug_visible', False))]
```

## Notes

- The property must exist and be boolean (or a value Python can negate with `not`). If it doesn't exist, raises `KeyError`.
- Initialize the property in `On Start` if it may not exist: `[Set Property: prop="active", Value=False]`.
