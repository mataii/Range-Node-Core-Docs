# Concat String

**Type:** Data  
**Category:** DATA

Concatenates two strings with an optional separator.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| A | String | `""` | First string |
| B | String | `""` | Second string |
| Separator | String | `""` | Text between A and B |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| A | Input | Data (String) |
| B | Input | Data (String) |
| Result | Output | Data (String) |

## Generated code

```python
str("Hello") + " " + str("World")
# result: "Hello World"
```

## Typical usage

### Build a dynamic file name

```
[On Start] → [Play Sound:
                 Sound File = {Concat String: A="music/", B={Get Property: prop="zone"}, Separator=""}]
```

### Display formatted score

```
[On Update] → [Set Text:
                  Text = {Concat String: A="Score: ", B={Get Property: prop="score"}}]
```

## Notes

- Applies `str()` to both operands, so it accepts any type Python can convert to a string.
- For more complex formatting, use [Format String](format-string.md).
