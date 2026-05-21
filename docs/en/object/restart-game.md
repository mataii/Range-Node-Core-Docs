# Restart Game

**Type:** Action  
**Category:** OBJECT

Restarts the entire game from the beginning using `Range.logic.restartGame()`. Reloads the initial scene and resets all state.

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |

## Generated code

```python
Range.logic.restartGame()
```

## Typical usage

### Restart from Game Over screen

```
[On Key Press: Key=ENTER, Mode=PRESSED] → [Restart Game]
```

### Restart after countdown

```
[On Timer: Duration=5.0] → [Restart Game]
```

## Notes

- `restartGame()` destroys all current state — including `globalDict`. To preserve data across restarts, use the [Save/Load](../save/save-game.md) system to save to disk first.
- Has no `Out` socket because execution ends at this point.
- To switch to another scene (without a full restart), use [Scene Switch](scene-switch.md).
