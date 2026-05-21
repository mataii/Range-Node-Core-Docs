# Pause Sound

**Tipo:** Acción  
**Categoría:** AUDIO

Pausa un sonido en reproducción. El handle sigue siendo válido — puede reanudarse con [Resume Sound](resume-sound.md).

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
    _aud_hdl.pause()
```

## Uso típico

### Pausar música al abrir menú

```
[On Message: Subject="pause_open"] → [Pause Sound: Handle Key="bgm"]
[On Message: Subject="pause_close"] → [Resume Sound: Handle Key="bgm"]
```

## Notas

- `pause()` congela la reproducción en la posición actual.
- Si el sonido ya terminó, `pause()` no tiene efecto (el handle es inválido).
