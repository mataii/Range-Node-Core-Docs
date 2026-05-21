# DIALOGUE

Nodes for managing dialogue sequences and subtitles in Range Game Engine. The system uses `globalDict` as a signal bus and stores dialogue state on the object that holds the `DialoguePlayer` component.

## Nodes

| Node | Type | Description |
|------|------|-------------|
| [Start Dialogue](start-dialogue.md) | Action | Start a dialogue sequence by ID |
| [Advance Dialogue](advance-dialogue.md) | Action | Manually advance to the next line |
| [Stop Dialogue](stop-dialogue.md) | Action | Force-stop a running dialogue |
| [Dialogue Player](dialogue-player.md) | Action (branch) | Main player — detects line changes |
| [On Dialogue End](on-dialogue-end.md) | Action (branch) | Fires for one frame when dialogue ends |
| [Dialogue Text](get-dialogue-text.md) | Data | Text of the current line |
| [Dialogue Speaker](get-dialogue-speaker.md) | Data | Speaker name for the current line |
| [Dialogue Index](get-dialogue-index.md) | Data | Zero-based index of the current line |
| [Is Dialogue Active](is-dialogue-active.md) | Data | True while dialogue is playing |
| [Show Subtitle](show-subtitle.md) | Action | Display timed text (subtitle with duration) |
| [Get Subtitle](get-subtitle.md) | Data | Return subtitle text if not yet expired |

## System architecture

```
Trigger → [Start Dialogue: ID="intro"]
               ↓ (globalDict: _dlg_start_intro = True)

[On Update] → [Dialogue Player: ID="intro", Advance=Spacebar]
                  └── Line Changed ──► [Set Text: Object=text_box, Text={Dialogue Text: ID="intro"}]
                                    ──► [Set Text: Object=speaker_label, Text={Dialogue Speaker: ID="intro"}]
                  └── Idle ──► [...]

[On Update] → [On Dialogue End: ID="intro"]
                  └── On End ──► [Overlay Scene: Scene=DialogueHUD, Mode=Remove]
                  └── Otherwise ──► [...]
```

## Notes

- `Dialogue Player` must be on an object with `On Update` — it is the only node that executes the advance logic.
- Data nodes (`Dialogue Text`, `Dialogue Speaker`, etc.) must be on the same object as `Dialogue Player`.
- For dialogues with more than 8 lines, chain two `DialoguePlayer`s using `On Dialogue End` → `Start Dialogue`.
