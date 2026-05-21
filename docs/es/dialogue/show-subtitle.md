# Show Subtitle

**Tipo:** Acción  
**Categoría:** DIALOGUE

Muestra un texto temporal (subtítulo) durante una duración determinada. El texto se guarda en `globalDict` junto a su tiempo de expiración; [Get Subtitle](get-subtitle.md) lo lee y lo muestra mientras no haya expirado.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Subtitle ID | String | `"sub1"` | Identificador compartido con Get Subtitle |
| Text | String | `""` | Texto a mostrar (usado si el socket Text no está conectado) |
| Duration (s) | Float | `3.0` | Duración en segundos antes de que el texto expire |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Text | Entrada | Dato (String) |
| Duration | Entrada | Dato (Float) |
| Out | Salida | Exec |

## Código generado

```python
Range.logic.globalDict['_sub_sub1'] = {
    'text': str("¡Cuidado!"),
    'until': Range.logic.getClockTime() + float(3.0),
}
```

## Uso típico

### Subtítulo de tutorial

```
[On Start]
    → [Show Subtitle: ID="hint", Text="Pulsa ESPACIO para saltar", Duration=5.0]

[On Update]
    → [Set Text: Object=subtitle_obj, Text={Get Subtitle: ID="hint"}]
```

### Subtítulo dinámico de cutscene

```
[On Message: Subject="scene_event"]
    → [Show Subtitle: ID="narrator", Text={Get Save Data: Key="narrator_line"}, Duration=4.0]
```

## Notas

- No muestra nada por sí solo — necesita un [Get Subtitle](get-subtitle.md) conectado a [Set Text](../ui/set-text.md) en `On Update`.
- Si se llama de nuevo antes de que expire, el texto y el temporizador se reinician.
- Para textos de diálogo con avance manual, usa [Dialogue Player](dialogue-player.md) en su lugar.
