# Dialogue Player

**Tipo:** Acción (rama)  
**Categoría:** DIALOGUE

Nodo principal del sistema de diálogo. Gestiona la reproducción de hasta 8 líneas, el avance y el estado del diálogo. Debe estar conectado a `On Update` para ejecutarse cada frame.

Emite la rama **Line Changed** durante exactamente un frame cada vez que la línea actual cambia. Emite **Idle** en todos los demás frames.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Dialogue ID | String | `"dlg1"` | Identificador compartido con Start/Stop/Advance/datos |
| Advance | Enum | `Spacebar` | Cómo avanza el diálogo: Spacebar, Enter, E Key, Left Click, Auto, Manual Only |
| Line 1–8 Speaker | String | `"NPC"` / `""` | Nombre del hablante (vacío = sin hablante) |
| Line 1–8 Text | String | — | Texto de la línea (línea vacía = fin del escaneo) |
| Line 1–8 Dur (s) | Float | `3.0` | Duración (solo con `Advance=Auto`) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Line Changed | Salida | Exec |
| Idle | Salida | Exec |

## Uso típico

### Diálogo completo con HUD

```
[On Update] → [Dialogue Player: ID="intro", Advance=Spacebar
                   Line 1: Speaker=NPC,    Text="Bienvenido, aventurero."
                   Line 2: Speaker=Player, Text="Hola."
                   Line 3: Speaker=NPC,    Text="Hay algo extraño aquí."]
                  └── Line Changed ──► [Set Text: Object=text_box,     Text={Dialogue Text: ID="intro"}]
                                    ──► [Set Text: Object=speaker_name, Text={Dialogue Speaker: ID="intro"}]
                  └── Idle ──► [...]
```

### Diálogo automático (subtítulos de cutscene)

```
[On Update] → [Dialogue Player: ID="scene1", Advance=Auto
                   Line 1: Speaker="", Text="Han pasado 100 años...", Dur=4.0
                   Line 2: Speaker="", Text="La oscuridad avanza.",   Dur=3.5]
                  └── Line Changed ──► [Set Text: Object=subtitle_obj, Text={Dialogue Text: ID="scene1"}]
                  └── Idle ──► [...]
```

## Notas

- El escaneo de líneas se detiene en la primera línea con `Text` vacío.
- Con `Advance=Manual Only`, solo [Advance Dialogue](advance-dialogue.md) puede avanzar las líneas.
- Los nodos de datos (`Dialogue Text`, `Dialogue Speaker`, etc.) deben estar en el mismo objeto que este nodo.
- Para más de 8 líneas, conecta [On Dialogue End](on-dialogue-end.md) → [Start Dialogue](start-dialogue.md) con un segundo `Dialogue Player`.
