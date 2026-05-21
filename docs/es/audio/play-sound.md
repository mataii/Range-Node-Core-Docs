# Play Sound

**Tipo:** Acción  
**Categoría:** AUDIO

Reproduce un archivo de audio usando el módulo `aud`. Almacena el handle de reproducción en `globalDict` para controlarlo posteriormente.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Sound File | String | `""` | Ruta al archivo de audio (relativa al blend o absoluta) |
| Handle Key | String | `"sound"` | Clave en `globalDict` para guardar el handle |
| Volume | Float | `1.0` | Volumen inicial [0.0 – 1.0] |
| Pitch | Float | `1.0` | Tono (1.0 = normal) |
| Pan | Float | `0.0` | Balance estéreo [-1.0 = izq, 1.0 = der] |
| Loop | Bool | `False` | Si es True, reproduce en bucle indefinido |
| 3D | Bool | `False` | Si es True, activa audio posicional 3D |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |
| Volume | Entrada | Dato (Float) |
| Pitch | Entrada | Dato (Float) |

## Código generado

```python
import aud
_aud_dev = aud.Device()
_aud_snd = aud.Sound.file('explosion.wav')
_aud_hdl = _aud_dev.play(_aud_snd)
_aud_hdl.volume = 1.0
_aud_hdl.pitch  = 1.0
_aud_hdl.loop   = -1 if True else 0  # Loop
Range.logic.globalDict['_aud_bgm'] = _aud_hdl
```

Para audio 3D se añade posición y orientación del objeto.

## Uso típico

### Música de fondo en bucle

```
[On Start] → [Play Sound:
                 Sound File = "music/theme.wav"
                 Handle Key = "bgm"
                 Volume = 0.8
                 Loop = True]
```

### Efecto de sonido de un disparo

```
[Weapon Fire Executor] → [Play Sound:
                              Sound File = "sfx/gunshot.wav"
                              Handle Key = "shot"
                              Pitch = {Math: random(0.9, 1.1)}]
```

## Notas

- La ruta del archivo es relativa al directorio del blend. Usa `//sfx/archivo.wav` para rutas relativas.
- Si `Loop = True`, el handle se mantiene activo indefinidamente — usa `Stop Sound` para detenerlo.
- Cada llamada crea un nuevo handle. Si usas la misma clave dos veces sin detener el anterior, el handle anterior queda huérfano.
