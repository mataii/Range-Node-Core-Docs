# Stop Sound

**Tipo:** Acción  
**Categoría:** AUDIO

Detiene un sonido en reproducción recuperando su handle desde `globalDict` y llamando a `handle.stop()`.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Handle Key | String | `"sound"` | Clave usada en `Play Sound` para este sonido |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
_aud_hdl = Range.logic.globalDict.get('_aud_bgm', None)
if _aud_hdl:
    _aud_hdl.stop()
```

## Uso típico

### Detener música al cambiar de escena

```
[On Key Press: Key=ESC, Mode=PRESSED]
    → [Stop Sound: Handle Key="bgm"]
    → [Scene Switch: Scene="MainMenu"]
```

### Detener efecto de sonido en bucle

```
[On State Exit: FSM ID="engine", State="running"]
    └── On Exit ──► [Stop Sound: Handle Key="engine_loop"]
```

## Notas

- Si la clave no existe en `globalDict` o el handle ya expiró, la llamada es ignorada de forma segura (no lanza error).
- Tras `stop()`, el handle queda inválido — no intentes `pause()` o `resume()` después.
