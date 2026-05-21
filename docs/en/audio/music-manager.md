# Music Manager

**Type:** Prefab (complete component)  
**Category:** AUDIO

Complete music manager with auto-start, fade in/out, and runtime track switching. Designed to declaratively handle scene background music.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Track File | String | `""` | Initial music file |
| Volume | Float | `0.8` | Maximum volume |
| Fade In | Float | `2.0` | Fade in duration in seconds |
| Fade Out | Float | `2.0` | Fade out duration when switching tracks |
| Loop | Bool | `True` | Loop playback |
| Track Key | String | `"bgm_track"` | BGE property on the object. When it changes, fades to the new track |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Behavior

The prefab generates code equivalent to:

1. **Init (first frame):** starts track playback at volume 0.
2. **Fade in:** increases volume by `volume += (target / fade_in) * deltaTime()` each frame.
3. **Track change detection:** monitors `self.own[track_key]`. When it changes:
   - Fades out the current track.
   - When volume reaches 0, loads and plays the new track with fade in.

## Typical usage

### Background music with fade in on start

```
[On Start] → [Music Manager:
                  Track File = "music/level1.wav"
                  Volume = 0.8
                  Fade In = 3.0]
```

### Change track dynamically

```
# On the object that has Music Manager, BGE property "bgm_track":
[On State Enter: FSM ID="zone", State="boss"]
    └── On Enter ──► [Set Property: prop="bgm_track", Value="music/boss.wav"]
```

## Notes

- Fade uses `Range.logic.getFrameTime()` (deltaTime) to be frame-rate independent.
- Can only manage one track at a time. For multiple audio layers, use [Play Sound](play-sound.md) with different handle keys.
- The object that holds `Music Manager` must remain alive for the scene duration — do not destroy it with `endObject()`.
