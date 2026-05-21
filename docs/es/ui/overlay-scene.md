# Overlay Scene

**Tipo:** Acción  
**Categoría:** UI

Añade, elimina o alterna una escena superpuesta en Range Game Engine. Las escenas overlay se renderizan encima de la escena activa — ideal para HUD, menús de pausa y pantallas de carga.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Scene | Enum | — | Escena a gestionar como overlay |
| Mode | Enum | `Add` | `Add` / `Remove` / `Toggle` |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
# Mode = Add
_ov_running = any(s.name == 'HUD' for s in Range.logic.getSceneList())
if not _ov_running:
    Range.logic.addScene('HUD', 1)

# Mode = Remove
for _s in Range.logic.getSceneList():
    if _s.name == 'HUD':
        _s.end()
        break

# Mode = Toggle
_ov_running = any(s.name == 'HUD' for s in Range.logic.getSceneList())
if _ov_running:
    for _s in Range.logic.getSceneList():
        if _s.name == 'HUD':
            _s.end()
            break
else:
    Range.logic.addScene('HUD', 1)
```

## Uso típico

### Mostrar HUD al iniciar

```
[On Start] → [Overlay Scene: Scene=HUD, Mode=Add]
```

### Menú de pausa con tecla Escape

```
[On Key Press: Key=ESC, Mode=PRESSED]
    → [Overlay Scene: Scene=PauseMenu, Mode=Toggle]
```

### Ocultar HUD en cutscene

```
[On Message: Subject="cutscene_start"]
    → [Overlay Scene: Scene=HUD, Mode=Remove]

[On Message: Subject="cutscene_end"]
    → [Overlay Scene: Scene=HUD, Mode=Add]
```

## Notas

- El modo `Add` no hace nada si la escena ya está activa (evita duplicados).
- El modo `Toggle` comprueba automáticamente si la escena está activa antes de añadir o eliminar.
- La escena overlay debe estar en el mismo proyecto `.blend` para aparecer en el selector.
- Las escenas overlay tienen su propio espacio de objetos pero comparten `globalDict`.
