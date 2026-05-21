# Set Save Data

**Tipo:** Acción  
**Categoría:** SAVE

Escribe un valor en `Range.logic.globalDict` con la clave indicada. Los datos permanecen en memoria hasta que se llame a [Save Game](save-game.md) para persistirlos a disco.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Key | String | `"player_health"` | Clave en globalDict |
| Default Value | String | `"0"` | Valor usado si el socket Value no está conectado |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Value | Entrada | Dato (cualquier tipo) |
| Out | Salida | Exec |

## Código generado

```python
Range.logic.globalDict['player_health'] = 100
```

## Uso típico

### Guardar puntuación

```
[On Update]
    → [Set Save Data: Key="score", Value={Get Property: prop="score"}]
```

### Guardar posición del jugador

```
[On Message: Subject="checkpoint"]
    → [Set Save Data: Key="checkpoint_x", Value={Object Position: Axis=X}]
    → [Set Save Data: Key="checkpoint_y", Value={Object Position: Axis=Y}]
    → [Set Save Data: Key="checkpoint_z", Value={Object Position: Axis=Z}]
```

## Notas

- Los datos solo existen en memoria hasta que se llame a [Save Game](save-game.md).
- Si la clave ya existe, su valor se sobreescribe.
- Alternativa más directa: [Property To Save](copy-property-to-save.md) copia una propiedad BGE completa de una sola vez.
