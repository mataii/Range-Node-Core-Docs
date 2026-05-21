# AUDIO Category

Nodes for playing and controlling sounds at runtime. Use the `aud` module from Range Game Engine.

## Available Nodes

| Node | Type | Description |
|------|------|-------------|
| [Play Sound](play-sound.md) | Action | Plays an audio file |
| [Stop Sound](stop-sound.md) | Action | Stops a playing sound |
| [Set Volume](set-volume.md) | Action | Changes the volume of a playing sound |
| [Pause Sound](pause-sound.md) | Action | Pauses a playing sound |
| [Resume Sound](resume-sound.md) | Action | Resumes a paused sound |
| [Music Manager](music-manager.md) | Prefab | Music manager with fade in/out and track switching |

## Handle System

`Play Sound` creates an audio handle and stores it in `Range.logic.globalDict` under a key (`Handle Key`). The `Stop Sound`, `Set Volume`, `Pause Sound`, and `Resume Sound` nodes retrieve that handle by the same key to control the sound. The handle persists between frames while the sound is active.

```
[On Start] → [Play Sound: File="music.wav", Handle Key="bgm", Loop=True]
[On Key Press: Key=M, Mode=PRESSED] → [Pause Sound: Handle Key="bgm"]
[On Key Press: Key=M, Mode=PRESSED] → [Resume Sound: Handle Key="bgm"]
```
