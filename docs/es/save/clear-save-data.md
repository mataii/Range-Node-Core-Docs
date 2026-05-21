# Clear Save Data

**Tipo:** Acción  
**Categoría:** SAVE

Borra todo el contenido de `Range.logic.globalDict`. Útil para reiniciar el estado de guardado o limpiar datos de una sesión anterior.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
Range.logic.globalDict.clear()
```

## Uso típico

### Borrar datos antes de iniciar nueva partida

```
[On Message: Subject="new_game"]
    → [Clear Save Data]
    → [Set Save Data: Key="health",  Value=100]
    → [Set Save Data: Key="score",   Value=0]
    → [Save Game: Filename="savegame"]
```

### Resetear tras borrar guardado del menú

```
[On Message: Subject="delete_save"]
    → [Clear Save Data]
    → [Save Game: Filename="savegame"]  # sobreescribe con dict vacío
```

## Notas

- Borra **todas** las claves en memoria. Si luego llamas a [Save Game](save-game.md), el archivo quedará vacío.
- Para borrar solo una clave específica, usa [Delete Save Data](delete-save-data.md).
- Esta acción no puede deshacerse una vez ejecutada.
