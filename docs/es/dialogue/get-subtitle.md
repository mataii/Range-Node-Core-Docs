# Get Subtitle

**Tipo:** Dato  
**Categoría:** DIALOGUE

Lee el texto del subtítulo activo con el ID indicado. Devuelve el texto mientras el subtítulo no haya expirado; devuelve cadena vacía en caso contrario.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Subtitle ID | String | `"sub1"` | Debe coincidir con el Show Subtitle correspondiente |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Text | Salida | Dato (String) |

## Código generado

```python
(lambda _s: _s['text'] if _s and Range.logic.getClockTime() < _s['until'] else '')
(Range.logic.globalDict.get('_sub_sub1'))
```

## Uso típico

```
[On Update]
    → [Set Text: Object=subtitle_obj, Text={Get Subtitle: ID="sub1"}]
```

Con este patrón, el objeto de texto muestra el subtítulo mientras está activo y muestra cadena vacía cuando expira — limpiando el texto automáticamente sin lógica adicional.

## Notas

- Conectar a [Set Text](../ui/set-text.md) es el patrón estándar.
- El texto se limpia automáticamente al expirar (sin necesidad de nodo extra).
- Pareja natural de [Show Subtitle](show-subtitle.md).
