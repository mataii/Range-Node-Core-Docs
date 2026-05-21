# Scene Switch

**Tipo:** Acción  
**Categoría:** OBJECT

Cambia a otra escena usando `Range.logic.startGame()`. Termina la escena actual y carga la nueva.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Scene | String | `"scene_name"` | Nombre del archivo de escena (con extensión `.blend`) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |

## Código generado

```python
Range.logic.startGame('level_2.blend')
```

## Uso típico

### Ir al menú principal

```
[On Key Press: Key=ESC, Mode=PRESSED] → [Scene Switch: Scene="MainMenu.blend"]
```

### Cargar siguiente nivel

```
[On Message: Subject="level_complete"] → [Scene Switch: Scene="Level2.blend"]
```

### Ir a pantalla de créditos

```
[On Timer: Duration=3.0] → [Scene Switch: Scene="Credits.blend"]
```

## Notas

- El nombre de escena debe incluir la extensión `.blend` y ser relativo al directorio del juego.
- `startGame()` destruye toda la escena actual, incluyendo `globalDict`. Para preservar datos, guárdalos en disco con [Save Game](../save/save-game.md) antes de cambiar de escena.
- No tiene socket `Out` porque la ejecución de la escena actual termina en este punto.
