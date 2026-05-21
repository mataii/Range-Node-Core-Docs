# String Length

**Type:** Data  
**Category:** DATA

Returns the length of a string as an integer.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Text | String | `""` | String to measure |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Text | Input | Data (String) |
| Length | Output | Data (Int) |

## Generated code

```python
len(str("hello"))
# result: 5
```

## Typical usage

### Validate a field is not empty

```
[On Update] → [BT Condition: {String Length: Text={Get Property: prop="username"}} > 0]
                  └── True ──► [...]  # valid field
```

### Limit input length

```
[On Update] → [BT Condition: {String Length: Text={Get Property: prop="input"}} < 20]
                  └── True ──► [...]  # can keep typing
```

## Notes

- Applies `str()` to the input, so it accepts numbers and other types.
- Returns `0` for empty strings.
