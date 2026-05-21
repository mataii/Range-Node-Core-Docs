# Mouse Button

**Tipo:** Rama (True / Otherwise)  
**Categoría:** MOUSE

Detecta el estado de un botón del ratón: activado (primer frame), activo (mantenido) o liberado.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Button | Enum | `Left` | `Left` / `Right` / `Middle` |
| Mode | Enum | `activated` | `activated` (primer frame) / `active` (mantenido) / `released` |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| True | Salida | Exec (condición cumplida) |
| Otherwise | Salida | Exec |

## Código generado

```python
if Range.logic.mouse.events[Range.events.LEFTMOUSE] == Range.logic.KX_INPUT_JUST_ACTIVATED:
    #TRUE_PATH#
else:
    #FALSE_PATH#
```

Constantes de evento según botón:

| Button | Constante |
|--------|-----------|
| Left | `Range.events.LEFTMOUSE` |
| Right | `Range.events.RIGHTMOUSE` |
| Middle | `Range.events.MIDDLEMOUSE` |

Constantes de estado según modo:

| Mode | Constante |
|------|-----------|
| activated | `KX_INPUT_JUST_ACTIVATED` |
| active | `KX_INPUT_ACTIVE` |
| released | `KX_INPUT_JUST_RELEASED` |

## Uso típico

### Disparar al hacer click izquierdo

```
[On Update] → [Mouse Button: Button=Left, Mode=active]
                  └── True ──► [Weapon Fire Executor]
```

### Interactuar con click derecho

```
[On Update] → [Mouse Button: Button=Right, Mode=activated]
                  └── True ──► [Interaction System]
```

### Confirmar selección al soltar

```
[On Update] → [Mouse Button: Button=Left, Mode=released]
                  └── True ──► [BTCustomTask: confirm_selection()]
```

## Notas

- `activated` dispara exactamente un frame al pulsar el botón.
- `active` dispara cada frame mientras el botón está presionado.
- `released` dispara exactamente un frame al soltar el botón.
