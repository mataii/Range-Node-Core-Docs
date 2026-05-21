# Categoría AUDIO

Nodos para reproducir y controlar sonidos en tiempo de ejecución. Usan el módulo `aud` de Range Game Engine.

## Nodos disponibles

| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Play Sound](play-sound.md) | Acción | Reproduce un archivo de audio |
| [Stop Sound](stop-sound.md) | Acción | Detiene un sonido en reproducción |
| [Set Volume](set-volume.md) | Acción | Cambia el volumen de un sonido en reproducción |
| [Pause Sound](pause-sound.md) | Acción | Pausa un sonido en reproducción |
| [Resume Sound](resume-sound.md) | Acción | Reanuda un sonido pausado |
| [Music Manager](music-manager.md) | Prefab | Gestor de música con fade in/out y cambio de pista |

## Sistema de handles

`Play Sound` crea un handle de audio y lo almacena en `Range.logic.globalDict` bajo una clave (`Handle Key`). Los nodos `Stop Sound`, `Set Volume`, `Pause Sound` y `Resume Sound` recuperan ese handle por la misma clave para controlar el sonido. El handle persiste entre frames mientras el sonido esté activo.

```
[On Start] → [Play Sound: File="music.wav", Handle Key="bgm", Loop=True]
[On Key Press: Key=M, Mode=PRESSED] → [Pause Sound: Handle Key="bgm"]
[On Key Press: Key=M, Mode=PRESSED] → [Resume Sound: Handle Key="bgm"]
```
