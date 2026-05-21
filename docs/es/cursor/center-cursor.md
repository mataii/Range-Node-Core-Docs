# Center Cursor

**Tipo:** Acción  
**Categoría:** CURSOR

Mueve el cursor al centro de la pantalla estableciendo `Range.logic.mouse.position = [0.5, 0.5]`.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
Range.logic.mouse.position = [0.5, 0.5]
```

## Uso típico

### Centrar cursor cada frame para mouselook

```
[On Update] → [Center Cursor]
             → [Capture Mouse: Capture ID="cam"]
```

Centrar el cursor cada frame evita que alcance los bordes de la pantalla, lo que causaría que el delta quede limitado.

### Centrar al entrar en modo FPS

```
[On State Enter: FSM ID="player", State="fps"]
    └── On Enter ──► [Set Cursor Visible: Mode=HIDE]
                  ──► [Center Cursor]
```

## Notas

- `mouse.position` acepta valores normalizados [0.0, 1.0] donde [0.5, 0.5] es el centro exacto de la ventana de juego.
- El centrado ocurre al final del frame en que se ejecuta — el movimiento del cursor en ese mismo frame ya fue registrado.
