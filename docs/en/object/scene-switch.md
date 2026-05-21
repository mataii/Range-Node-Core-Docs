# Scene Switch

**Type:** Action  
**Category:** OBJECT

Switches to another scene using `Range.logic.startGame()`. Ends the current scene and loads the new one.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Scene | String | `"scene_name"` | Scene filename (with `.blend` extension) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |

## Generated code

```python
Range.logic.startGame('level_2.blend')
```

## Typical usage

### Go to main menu

```
[On Key Press: Key=ESC, Mode=PRESSED] → [Scene Switch: Scene="MainMenu.blend"]
```

### Load next level

```
[On Message: Subject="level_complete"] → [Scene Switch: Scene="Level2.blend"]
```

### Go to credits screen

```
[On Timer: Duration=3.0] → [Scene Switch: Scene="Credits.blend"]
```

## Notes

- The scene name must include the `.blend` extension and be relative to the game directory.
- `startGame()` destroys the entire current scene, including `globalDict`. To preserve data, save to disk with [Save Game](../save/save-game.md) before switching.
- Has no `Out` socket because execution of the current scene ends at this point.
