# Number To String

**Type:** Data  
**Category:** DATA

Converts a number to a string with optional prefix, suffix, and decimal control.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Value | Float | `0.0` | Number to convert |
| Prefix | String | `""` | Text before the number |
| Suffix | String | `""` | Text after the number |
| Decimals | Int | `2` | Number of decimal places. `0` = no decimals |
| As Int | Bool | `False` | If True, converts to integer before formatting |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| Value | Input | Data (Float/Int) |
| Result | Output | Data (String) |

## Generated code

```python
# Decimals=2, Prefix="$", Suffix=" USD"
"$" + f"{42.5:.2f}" + " USD"
# result: "$42.50 USD"

# As Int=True
"$" + str(int(42.5)) + " USD"
# result: "$42 USD"
```

## Typical usage

### Display speed in km/h

```
[On Update] → [Set Text:
                  Text = {Number To String:
                              Value = {Vehicle Speed: km/h}
                              Suffix = " km/h"
                              Decimals = 0}]
```

### Display health percentage

```
[On Update] → [Set Text:
                  Text = {Number To String:
                              Value = {Get Property: prop="health"}
                              Suffix = "%"
                              As Int = True}]
```

## Notes

- With `Decimals=0` and `As Int=False`, may produce `42.0` depending on implementation. Use `As Int=True` for a clean integer.
