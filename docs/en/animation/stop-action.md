# Stop Action

**Type:** Action  
**Category:** ANIMATION

Stops animation on a specific layer using `stopAction()`.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Layer | Int | `0` | Animation layer to stop (0–7) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
self.own.stopAction(0)
```

## Typical usage

### Stop fire animation

```
[On State Exit: FSM ID="weapon", State="firing"]
    └── On Exit ──► [Stop Action: Layer=1]
```

### Stop all layers on pause

```
[On Message: Subject="game_paused"]
    → [Stop Action: Layer=0]
    → [Stop Action: Layer=1]
    → [Stop Action: Layer=2]
```

## Notes

- `stopAction(layer)` freezes the object at the current frame of that layer.
- To resume animation from where it stopped, there is no `resumeAction` — you must use `playAction` with the current frame as the start point.
- Stopping an inactive layer produces no error.
