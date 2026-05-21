# DIALOGUE

Nodos para gestionar sistemas de diálogo y subtítulos en Range Game Engine. El sistema usa `globalDict` como señal y guarda el estado del diálogo en el objeto que tiene el componente `DialoguePlayer`.

## Nodos

| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Start Dialogue](start-dialogue.md) | Acción | Inicia una secuencia de diálogo por ID |
| [Advance Dialogue](advance-dialogue.md) | Acción | Avanza manualmente a la siguiente línea |
| [Stop Dialogue](stop-dialogue.md) | Acción | Detiene el diálogo forzosamente |
| [Dialogue Player](dialogue-player.md) | Acción (rama) | Reproductor principal — detecta cambios de línea |
| [On Dialogue End](on-dialogue-end.md) | Acción (rama) | Se dispara un frame cuando el diálogo termina |
| [Dialogue Text](get-dialogue-text.md) | Dato | Texto de la línea actual |
| [Dialogue Speaker](get-dialogue-speaker.md) | Dato | Nombre del hablante de la línea actual |
| [Dialogue Index](get-dialogue-index.md) | Dato | Índice (0-based) de la línea actual |
| [Is Dialogue Active](is-dialogue-active.md) | Dato | True si el diálogo está en reproducción |
| [Show Subtitle](show-subtitle.md) | Acción | Muestra texto temporal (subtítulo con duración) |
| [Get Subtitle](get-subtitle.md) | Dato | Devuelve el texto del subtítulo si aún no expiró |

## Arquitectura del sistema

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

## Notas

- `Dialogue Player` debe estar en un objeto con `On Update` — es el único que ejecuta la lógica de avance.
- Los nodos de datos (`Dialogue Text`, `Dialogue Speaker`, etc.) deben estar en el mismo objeto que `Dialogue Player`.
- Para diálogos de más de 8 líneas, encadena dos `DialoguePlayer` usando `On Dialogue End` → `Start Dialogue`.
