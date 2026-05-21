# Gamepad Button

**Tipo:** Rama (Pressed / Released / Held / Otherwise)  
**Categoría:** GAMEPAD

Detecta el estado de un botón de gamepad: pulsado (primer frame), mantenido (cada frame mientras esté activo) o liberado (primer frame al soltar).

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Gamepad Index | Int | `0` | Slot del dispositivo (0–7) |
| Button Index | Int | `0` | Índice del botón según el driver del SO |
| Mode | Enum | `HELD` | `PRESSED` / `HELD` / `RELEASED` |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| True | Salida | Exec (condición cumplida) |
| Otherwise | Salida | Exec (condición no cumplida) |

## Código generado

```python
_gp = Range.logic.joysticks[0]
if _gp is not None:
    _gp_btn = 0 in _gp.activeButtons
    # HELD:
    if _gp_btn:
        #TRUE_PATH#
    else:
        #FALSE_PATH#
```

Para `PRESSED` y `RELEASED` usa un flag previo en `globalDict`:

```python
_gp = Range.logic.joysticks[0]
if _gp is not None:
    _gp_btn  = 0 in _gp.activeButtons
    _gp_prev = Range.logic.globalDict.get('_gp0_prev_0', False)
    Range.logic.globalDict['_gp0_prev_0'] = _gp_btn
    # PRESSED: _gp_btn and not _gp_prev
    # RELEASED: not _gp_btn and _gp_prev
```

## Uso típico

### Saltar con botón A (Xbox)

```
[On Update] → [Gamepad Button: Index=0, Button=0, Mode=PRESSED]
                  └── True ──► [Player Jump]
```

### Disparar manteniendo gatillo

```
[On Update] → [Gamepad Button: Index=0, Button=7, Mode=HELD]
                  └── True ──► [Weapon Fire Executor]
```

### Recargar al soltar botón

```
[On Update] → [Gamepad Button: Index=0, Button=2, Mode=RELEASED]
                  └── True ──► [Weapon Reload]
```

## Notas

- Los índices de botones varían según el modelo de mando y el driver del sistema operativo. Compruébalos con `Print: {_gp.activeButtons}` durante el desarrollo.
- Si el gamepad no está conectado (`_gp is None`), el nodo cae a `Otherwise` sin error.
