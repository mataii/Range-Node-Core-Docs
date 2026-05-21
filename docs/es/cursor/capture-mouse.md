# Capture Mouse

**Tipo:** Acción  
**Categoría:** CURSOR

Captura el movimiento del ratón cada frame y almacena el delta en atributos del objeto. Diseñado para implementar mouselook sin necesidad de código manual.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Capture ID | String | `"cam"` | Identificador único de esta captura |
| Sensitivity | Float | `0.3` | Multiplicador del delta |
| Invert X | Bool | `False` | Invierte el eje horizontal |
| Invert Y | Bool | `False` | Invierte el eje vertical |
| Hide Cursor | Bool | `True` | Oculta el cursor automáticamente al activarse |
| Clamp | Float | `90.0` | Límite de pitch en grados (0 = sin límite) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado (simplificado)

```python
_mc_pos = Range.logic.mouse.position
_mc_cx, _mc_cy = 0.5, 0.5
self._mc_cam_dx = (_mc_pos[0] - _mc_cx) * 0.3
self._mc_cam_dy = (_mc_pos[1] - _mc_cy) * 0.3
Range.logic.mouse.position = [_mc_cx, _mc_cy]
if True:  # hide_cursor
    Range.logic.mouse.visible = False
```

Los deltas se almacenan en `self._mc_<capture_id>_dx` y `self._mc_<capture_id>_dy`.

## Uso típico

### Mouselook básico (primera persona)

```
[On Update] → [Capture Mouse: Capture ID="player", Sensitivity=0.3]
[On Update] → [BTCustomTask:
                   dx = {Get Mouse Delta: Capture ID="player", Axis=X}
                   dy = {Get Mouse Delta: Capture ID="player", Axis=Y}
                   self.own.applyRotation([0, 0, -dx], False)
                   cam = scene.objects['Camera']
                   cam.applyRotation([dy, 0, 0], True)]
```

## Notas

- `Capture Mouse` debe ejecutarse en el **mismo objeto** que `Get Mouse Delta` para que lean el mismo atributo.
- El cursor se centra automáticamente cada frame (escribe `mouse.position = [0.5, 0.5]`).
- Si `Hide Cursor = False`, el cursor seguirá siendo visible y puede salir de la ventana.
- Para múltiples fuentes de captura en el mismo objeto, usa diferentes `Capture ID`.
