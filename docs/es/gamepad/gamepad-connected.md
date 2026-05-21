# Gamepad Connected

**Tipo:** Rama (Connected / Not Connected)  
**Categoría:** GAMEPAD

Comprueba si hay un gamepad conectado en el slot indicado. Útil para adaptar controles o mostrar mensajes de "conecta un mando".

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Gamepad Index | Int | `0` | Slot del dispositivo (0–7) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Connected | Salida | Exec (gamepad conectado) |
| Not Connected | Salida | Exec (sin gamepad) |

## Código generado

```python
if Range.logic.joysticks[0] is not None:
    #TRUE_PATH#
else:
    #FALSE_PATH#
```

## Uso típico

### Alternar entre controles de teclado y gamepad

```
[On Update] → [Gamepad Connected: Index=0]
                  ├── Connected ──► [Gamepad Axis: ...]
                  └── Not Connected ──► [Player Input: ...]
```

### Mostrar aviso si no hay mando

```
[On Start] → [Gamepad Connected: Index=0]
                 └── Not Connected ──► [Node Broadcast: Subject="show_controller_warning"]
```

## Notas

- La verificación es O(1) — solo comprueba si el slot es `None`.
- Si el usuario desconecta el mando durante el juego, el slot puede pasar a `None` en cualquier frame.
