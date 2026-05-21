# Mouse Scroll

**Tipo:** Rama (Up / Down / Otherwise)  
**Categoría:** MOUSE

Detecta el desplazamiento de la rueda del ratón hacia arriba o hacia abajo.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Up | Salida | Exec (rueda hacia arriba) |
| Down | Salida | Exec (rueda hacia abajo) |
| Otherwise | Salida | Exec (sin desplazamiento) |

## Código generado

```python
_ms_up   = Range.logic.mouse.events[Range.events.WHEELUPMOUSE]   == Range.logic.KX_INPUT_JUST_ACTIVATED
_ms_down = Range.logic.mouse.events[Range.events.WHEELDOWNMOUSE] == Range.logic.KX_INPUT_JUST_ACTIVATED
if _ms_up:
    #UP_PATH#
elif _ms_down:
    #DOWN_PATH#
else:
    #OTHERWISE_PATH#
```

## Uso típico

### Cambiar slot de inventario con rueda

```
[On Update] → [Mouse Scroll]
                  ├── Up ──► [BTCustomTask: self.own['slot'] = max(0, self.own.get('slot',0)-1)]
                  └── Down ──► [BTCustomTask: self.own['slot'] = min(9, self.own.get('slot',0)+1)]
```

### Zoom de cámara con rueda

```
[On Update] → [Mouse Scroll]
                  ├── Up ──► [BTCustomTask: cam.lens = min(80, cam.lens + 2)]
                  └── Down ──► [BTCustomTask: cam.lens = max(20, cam.lens - 2)]
```

## Notas

- `WHEELUPMOUSE` y `WHEELDOWNMOUSE` son eventos de un solo frame — solo se activan el frame en que ocurre el desplazamiento.
- No hay un estado "mantenido" para la rueda del ratón.
