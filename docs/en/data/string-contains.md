# String Contains

**Type:** Branch (Found / Not Found)  
**Category:** DATA

Detects whether a string contains another string as a substring.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Text | String | `""` | String to search in |
| Substring | String | `""` | String to search for within Text |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Text | Input | Data (String) |
| Found | Output | Exec (substring found) |
| Not Found | Output | Exec (substring not found) |

## Generated code

```python
if "hello" in str("hello world"):
    #FOUND_PATH#
else:
    #NOT_FOUND_PATH#
```

## Typical usage

### Check tag in object name

```
[On Update] → [String Contains:
                  Text = {Get Property: prop="tag"}
                  Substring = "enemy"]
                  └── Found ──► [...]
```

### Filter messages by subject content

```
[On Message: Subject="cmd_*"] → [String Contains:
                                      Text = {Get Property: prop="msg"}
                                      Substring = "fire"]
                                      └── Found ──► [Weapon Fire Executor]
```

## Notes

- Search is case-sensitive. `"Hello"` does not contain `"hello"`.
- For exact comparison, use [String Equal](string-equal.md).
