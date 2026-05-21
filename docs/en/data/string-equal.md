# String Equal

**Type:** Branch (Equal / Not Equal)  
**Category:** DATA

Compares two strings for exact equality.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| A | String | `""` | First string |
| B | String | `""` | Second string to compare |
| Case Sensitive | Bool | `True` | If False, ignores case |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| A | Input | Data (String) |
| Equal | Output | Exec (strings are equal) |
| Not Equal | Output | Exec (strings differ) |

## Generated code

```python
# Case Sensitive = True
if str("hello") == str("hello"):
    #EQUAL_PATH#
else:
    #NOT_EQUAL_PATH#

# Case Sensitive = False
if str("hello").lower() == str("Hello").lower():
    #EQUAL_PATH#
```

## Typical usage

### Check object name

```
[On Collision] → [String Equal: A={self.own.name}, B="Player"]
                     └── Equal ──► [...]
```

### Check string property value

```
[On Update] → [String Equal:
                  A = {Get Property: prop="mode"}
                  B = "combat"
                  Case Sensitive = False]
                  └── Equal ──► [...]
```

## Notes

- To check if a value is in a list, use `BT Condition` with the `in` operator directly in `BT Custom Task`.
- `Case Sensitive = False` converts both strings to lowercase before comparing.
