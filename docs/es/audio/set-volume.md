# Set Volume

**Tipo:** Acción  
**Categoría:** AUDIO

Cambia el volumen de un sonido en reproducción recuperando su handle desde `globalDict`.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Handle Key | String | `"sound"` | Clave del handle en `globalDict` |
| Volume | Float | `1.0` | Nuevo volumen [0.0 – 1.0] |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |
| Volume | Entrada | Dato (Float) |

## Código generado

```python
_aud_hdl = Range.logic.globalDict.get('_aud_bgm', None)
if _aud_hdl:
    _aud_hdl.volume = 0.5
```

## Uso típico

### Bajar volumen en zona de silencio

```
[On Collision: Property="quiet_zone"] → [Set Volume: Handle Key="bgm", Volume=0.2]
```

### Fade out manual con timer

```
[On Update] → [BTCustomTask:
                   t = {Get State Time: FSM ID="fade"}
                   vol = max(0.0, 1.0 - t / 3.0)
              ] → [Set Volume: Handle Key="bgm", Volume={vol}]
```

## Notas

- Para fades automáticos, usa [Music Manager](music-manager.md) que implementa fade con `deltaTime()` de forma integrada.
- Cambiar el volumen de un handle no afecta a otros handles aunque usen el mismo archivo.
