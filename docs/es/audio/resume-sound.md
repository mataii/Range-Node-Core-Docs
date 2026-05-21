# Resume Sound

**Tipo:** Acción  
**Categoría:** AUDIO

Reanuda un sonido pausado desde donde se detuvo.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Handle Key | String | `"sound"` | Clave del handle en `globalDict` |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
_aud_hdl = Range.logic.globalDict.get('_aud_bgm', None)
if _aud_hdl:
    _aud_hdl.resume()
```

## Uso típico

### Reanudar música al cerrar menú de pausa

```
[On Message: Subject="pause_close"] → [Resume Sound: Handle Key="bgm"]
```

## Notas

- Solo funciona si el sonido fue pausado previamente con [Pause Sound](pause-sound.md). Si el handle es inválido o el sonido ya terminó, no tiene efecto.
