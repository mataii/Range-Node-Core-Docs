# Music Manager

**Tipo:** Prefab (componente completo)  
**Categoría:** AUDIO

Gestor de música completo con inicio automático, fade in/out y cambio de pista en tiempo de ejecución. Diseñado para manejar la música de fondo de una escena de forma declarativa.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Track File | String | `""` | Archivo de música inicial |
| Volume | Float | `0.8` | Volumen máximo |
| Fade In | Float | `2.0` | Duración del fade in en segundos |
| Fade Out | Float | `2.0` | Duración del fade out al cambiar de pista |
| Loop | Bool | `True` | Reproduce en bucle |
| Track Key | String | `"bgm_track"` | Propiedad BGE del objeto. Si cambia, hace fade al nuevo track |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Comportamiento

El prefab genera código equivalente a:

1. **Init (primer frame):** inicia la reproducción del track con volumen 0.
2. **Fade in:** incrementa el volumen `volume += (target / fade_in) * deltaTime()` cada frame.
3. **Detección de cambio de track:** monitoriza `self.own[track_key]`. Si cambia:
   - Hace fade out de la pista actual.
   - Cuando el volumen llega a 0, carga y reproduce el nuevo track con fade in.

## Uso típico

### Música de fondo con fade in al iniciar

```
[On Start] → [Music Manager:
                  Track File = "music/level1.wav"
                  Volume = 0.8
                  Fade In = 3.0]
```

### Cambiar pista dinámicamente

```
# En el objeto que tiene Music Manager, propiedad BGE "bgm_track":
[On State Enter: FSM ID="zone", State="boss"]
    └── On Enter ──► [Set Property: prop="bgm_track", Value="music/boss.wav"]
```

## Notas

- El fade usa `Range.logic.getFrameTime()` (deltaTime) para ser independiente del frame rate.
- Solo puede gestionar una pista a la vez. Para múltiples capas de audio, usa [Play Sound](play-sound.md) con diferentes handle keys.
- El objeto que tiene `Music Manager` debe existir mientras dure la escena — no lo destruyas con `endObject()`.
