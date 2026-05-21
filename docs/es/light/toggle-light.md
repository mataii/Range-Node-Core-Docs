# Toggle Light

**Tipo:** Acción  
**Categoría:** LIGHT

Activa, desactiva o alterna la visibilidad de una luz de la escena. Usa `setVisible()` en el objeto luz.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Light Object | String | `""` | Nombre del objeto luz. Vacío = `self.own` |
| Mode | Enum | `TOGGLE` | `ON` = activa, `OFF` = desactiva, `TOGGLE` = alterna |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
# Mode = ON
scene.objects['lamp_main'].setVisible(True)

# Mode = OFF
scene.objects['lamp_main'].setVisible(False)

# Mode = TOGGLE
_tl_obj = scene.objects['lamp_main']
_tl_obj.setVisible(not _tl_obj.visible)
```

## Uso típico

### Luz de habitación al entrar

```
[On Collision: Object="player"] → [Toggle Light: Light Object="room_lamp", Mode=ON]
```

### Parpadeo de luz con timer

```
[On Timer: Duration=0.1, Repeat=True] → [Toggle Light: Light Object="flicker_lamp", Mode=TOGGLE]
```

### Apagar todas las luces al morir

```
[On Message: Subject="player_died"] → [Toggle Light: Light Object="lamp_1", Mode=OFF]
                                    → [Toggle Light: Light Object="lamp_2", Mode=OFF]
```

## Notas

- `setVisible(False)` oculta el objeto y su emisión de luz. La luz deja de iluminar la escena.
- El modo `TOGGLE` lee la propiedad `.visible` del objeto en el momento de ejecución — puede producir parpadeo si se llama varias veces por frame.
