# Dialogue Player

**Type:** Action (branch)  
**Category:** DIALOGUE

The main node of the dialogue system. Manages playback of up to 8 lines, advancing, and dialogue state. Must be connected to `On Update` to run every frame.

Emits the **Line Changed** branch for exactly one frame each time the current line changes. Emits **Idle** on all other frames.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Dialogue ID | String | `"dlg1"` | Shared identifier with Start/Stop/Advance and data nodes |
| Advance | Enum | `Spacebar` | How the dialogue advances: Spacebar, Enter, E Key, Left Click, Auto, Manual Only |
| Line 1–8 Speaker | String | `"NPC"` / `""` | Speaker name (empty = no speaker) |
| Line 1–8 Text | String | — | Line text (empty line stops scanning) |
| Line 1–8 Dur (s) | Float | `3.0` | Duration (only used with `Advance=Auto`) |

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Line Changed | Output | Exec |
| Idle | Output | Exec |

## Typical usage

### Full dialogue with HUD

```
[On Update] → [Dialogue Player: ID="intro", Advance=Spacebar
                   Line 1: Speaker=NPC,    Text="Welcome, adventurer."
                   Line 2: Speaker=Player, Text="Hello."
                   Line 3: Speaker=NPC,    Text="Something strange is going on."]
                  └── Line Changed ──► [Set Text: Object=text_box,     Text={Dialogue Text: ID="intro"}]
                                    ──► [Set Text: Object=speaker_name, Text={Dialogue Speaker: ID="intro"}]
                  └── Idle ──► [...]
```

### Automatic dialogue (cutscene subtitles)

```
[On Update] → [Dialogue Player: ID="scene1", Advance=Auto
                   Line 1: Speaker="", Text="100 years have passed...", Dur=4.0
                   Line 2: Speaker="", Text="The darkness spreads.",    Dur=3.5]
                  └── Line Changed ──► [Set Text: Object=subtitle_obj, Text={Dialogue Text: ID="scene1"}]
                  └── Idle ──► [...]
```

## Notes

- Line scanning stops at the first line with an empty `Text` field.
- With `Advance=Manual Only`, only [Advance Dialogue](advance-dialogue.md) can advance lines.
- Data nodes (`Dialogue Text`, `Dialogue Speaker`, etc.) must be on the same object as this node.
- For more than 8 lines, connect [On Dialogue End](on-dialogue-end.md) → [Start Dialogue](start-dialogue.md) with a second `Dialogue Player`.
