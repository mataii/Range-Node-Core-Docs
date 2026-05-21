# Format String

**Type:** Data  
**Category:** DATA

Formats a template string with up to 4 values using Python's `.format()` syntax.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Template | String | `"{0}"` | String with `{0}`, `{1}`, `{2}`, `{3}` placeholders |
| Value 0 | Any | `""` | First value |
| Value 1 | Any | `""` | Second value |
| Value 2 | Any | `""` | Third value |
| Value 3 | Any | `""` | Fourth value |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Value 0–3 | Input | Data |
| Result | Output | Data (String) |

## Generated code

```python
"HP: {0} / {1}".format(75, 100)
# result: "HP: 75 / 100"
```

## Typical usage

### Display health and max health

```
[On Update] → [Set Text:
                  Text = {Format String:
                              Template = "HP: {0} / {1}"
                              Value 0 = {Get Property: prop="health"}
                              Value 1 = 100}]
```

### Build a dynamic save key

```
[BTCustomTask:
    key = {Format String: Template="save_slot_{0}", Value 0={Get Property: prop="slot"}}
    Range.logic.globalDict[key] = data]
```

## Notes

- Uses Python `str.format()` — supports all its syntax: `{0:.2f}` for 2 decimal places, `{0:03d}` for zero-padded integers.
- Unused values (no placeholder in template) are ignored.
