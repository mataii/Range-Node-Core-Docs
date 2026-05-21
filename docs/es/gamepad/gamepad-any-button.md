# Gamepad Any Button

**Tipo:** Rama (Any / None)  
**Categoría:** GAMEPAD

Detecta si cualquier botón del gamepad está siendo pulsado en el frame actual.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Gamepad Index | Int | `0` | Slot del dispositivo (0–7) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Any | Salida | Exec (al menos un botón activo) |
| None | Salida | Exec (ningún botón activo) |

## Código generado

```python
_gp = Range.logic.joysticks[0]
if _gp is not None and len(_gp.activeButtons) > 0:
    #TRUE_PATH#
else:
    #FALSE_PATH#
```

## Uso típico

### Salir de pantalla de inicio al pulsar cualquier botón

```
[On Update] → [Gamepad Any Button: Index=0]
                  └── Any ──► [Scene Switch: Scene="MainMenu"]
```

### Activar modo gamepad dinámicamente

```
[On Update] → [Gamepad Any Button: Index=0]
                  └── Any ──► [Set Property: prop="input_mode", Value="gamepad"]
```

## Notas

- `activeButtons` es una lista de índices de botones activos. `len() > 0` indica que al menos uno está activo.
- No distingue entre pulsado/mantenido — detecta cualquier botón activo en el frame actual. Usa [Gamepad Button](gamepad-button.md) con `Mode=PRESSED` si necesitas detección solo en el primer frame.
