# On Dialogue End

**Tipo:** Acción (rama)  
**Categoría:** DIALOGUE

Se dispara durante exactamente **un frame** cuando un diálogo pasa de activo a inactivo (última línea reproducida). Debe estar en el mismo objeto que el [Dialogue Player](dialogue-player.md) correspondiente.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Dialogue ID | String | `"dlg1"` | Debe coincidir con el Dialogue Player |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| On End | Salida | Exec |
| Otherwise | Salida | Exec |

## Código generado

```python
_dlge_cur  = getattr(self, '_dlg_active_dlg1', False)
_dlge_prev = getattr(self, '_dlg_was_active_dlg1', False)
self._dlg_was_active_dlg1 = _dlge_cur
if not _dlge_cur and _dlge_prev:
    # rama On End
else:
    # rama Otherwise
```

## Uso típico

### Cerrar HUD al terminar el diálogo

```
[On Update] → [On Dialogue End: Dialogue ID="intro"]
                  └── On End ──► [Overlay Scene: Scene=DialogueHUD, Mode=Remove]
                               ──► [Send Message: Subject="resume_gameplay"]
                  └── Otherwise ──► [...]
```

### Encadenar dos diálogos

```
[On Update] → [On Dialogue End: Dialogue ID="part1"]
                  └── On End ──► [Start Dialogue: Dialogue ID="part2"]
                  └── Otherwise ──► [...]
```

## Notas

- Detecta el flanco descendente de `_dlg_active` — solo se dispara en la transición, no mientras el diálogo está inactivo.
- No se dispara si el diálogo fue interrumpido por [Stop Dialogue](stop-dialogue.md) (este limpia el estado directamente).
- Debe conectarse a `On Update` para ejecutarse cada frame.
