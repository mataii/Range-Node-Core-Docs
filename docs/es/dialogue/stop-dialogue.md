# Stop Dialogue

**Tipo:** Acción  
**Categoría:** DIALOGUE

Detiene forzosamente un diálogo activo y limpia las señales pendientes en `globalDict`. Útil para interrumpir un diálogo desde un evento externo.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Dialogue ID | String | `"dlg1"` | Debe coincidir con el Dialogue Player a detener |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
if hasattr(self, '_dlg_active_dlg1'):
    self._dlg_active_dlg1 = False
Range.logic.globalDict.pop('_dlg_start_dlg1', None)
Range.logic.globalDict.pop('_dlg_adv_dlg1',   None)
```

## Uso típico

### Interrumpir diálogo al recibir daño

```
[On Message: Subject="take_damage"]
    → [Stop Dialogue: Dialogue ID="intro"]
    → [Overlay Scene: Scene=DialogueHUD, Mode=Remove]
```

### Cancelar diálogo al pulsar Escape

```
[On Key Press: Key=ESC, Mode=PRESSED]
    → [Stop Dialogue: Dialogue ID="intro"]
```

## Notas

- `Stop Dialogue` pone `_dlg_active` a `False` directamente — el nodo [On Dialogue End](on-dialogue-end.md) **no** se dispara en este caso, porque no se produce la transición activo → inactivo en el flujo normal del Player.
- Para ejecutar lógica de cleanup, conecta explícitamente después de `Stop Dialogue`.
