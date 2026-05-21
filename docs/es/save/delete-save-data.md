# Delete Save Data

**Tipo:** Acción  
**Categoría:** SAVE

Elimina una clave de `Range.logic.globalDict`. Si la clave no existe, no hace nada.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Key | String | `"player_health"` | Clave a eliminar de globalDict |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
if 'player_health' in Range.logic.globalDict:
    del Range.logic.globalDict['player_health']
```

## Uso típico

### Borrar dato temporal al salir de nivel

```
[On Message: Subject="level_end"]
    → [Delete Save Data: Key="temp_flag"]
```

### Invalidar sesión al morir

```
[On Message: Subject="game_over"]
    → [Delete Save Data: Key="current_run_score"]
```

## Notas

- El borrado es solo en memoria. Para que el cambio persista a disco, llama a [Save Game](save-game.md) después.
- Para eliminar todas las claves de una vez, usa [Clear Save Data](clear-save-data.md).
