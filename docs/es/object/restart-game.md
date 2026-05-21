# Restart Game

**Tipo:** Acción  
**Categoría:** OBJECT

Reinicia el juego completo desde el inicio usando `Range.logic.restartGame()`. Recarga la escena inicial y reinicia todo el estado.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |

## Código generado

```python
Range.logic.restartGame()
```

## Uso típico

### Reiniciar desde pantalla de Game Over

```
[On Key Press: Key=ENTER, Mode=PRESSED] → [Restart Game]
```

### Reiniciar tras cuenta regresiva

```
[On Timer: Duration=5.0] → [Restart Game]
```

## Notas

- `restartGame()` destruye todo el estado actual — `globalDict` incluido. No es posible preservar datos entre reinicios a menos que uses el sistema [Save/Load](../save/save-game.md) para guardar en disco primero.
- No tiene socket `Out` porque la ejecución del juego termina en este punto.
- Para cambiar a otra escena (sin reiniciar desde cero), usa [Scene Switch](scene-switch.md).
