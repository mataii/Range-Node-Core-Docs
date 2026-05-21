# Print

**Type:** Action  
**Category:** DEBUG

Prints a message to the Range Game Engine console. Equivalent to Python's `print()`.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Message | String | `"debug"` | Text to print. Accepts connected data sockets |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Generated code

```python
print("debug")
```

When a data socket is connected to the message:

```python
print(self.own.get('score', None))
```

## Typical usage

### Inspect a BGE property

```
[On Update] → [Print: Message = {Get Property: prop="score"}]
```

### Confirm a block executes

```
[On Start] → [Print: Message = "init OK"] → [...]
```

### Display FSM state

```
[On Update] → [Print: Message = {Get State: FSM ID="player"}]
```

## Notes

- Output appears in the system console (terminal) where Range Game Engine was launched.
- Has no effect on game logic. Remove before shipping.
- To print numeric values, connect them directly — Python converts them to string automatically.
