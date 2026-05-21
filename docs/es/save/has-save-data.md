# Has Save Data

**Tipo:** Acción (rama)  
**Categoría:** SAVE

Comprueba si una clave existe en `Range.logic.globalDict` y ramifica la ejecución según el resultado.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Key | String | `"player_health"` | Clave a buscar en globalDict |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Found | Salida | Exec |
| Not Found | Salida | Exec |

## Código generado

```python
if 'player_health' in Range.logic.globalDict:
    # rama Found
else:
    # rama Not Found
```

## Uso típico

### Detectar si hay partida guardada al iniciar

```
[On Start]
    → [Has Save Data: Key="save_version"]
          └── Found     ──► [Load Game: Filename="savegame"]
          └── Not Found ──► [Set Save Data: Key="save_version", Value=1]
```

### Condición antes de leer un dato

```
[On Update]
    → [Has Save Data: Key="high_score"]
          └── Found     ──► [Set Text: Text={Get Save Data: Key="high_score"}]
          └── Not Found ──► [Set Text: Text="---"]
```

## Notas

- Solo verifica si la clave existe en memoria (`globalDict`), no en el archivo de disco.
- Para comprobar si el archivo de guardado existe en disco, usa [Save Exists](save-exists.md).
