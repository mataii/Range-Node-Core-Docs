# Stop Action

**Tipo:** Acción  
**Categoría:** ANIMATION

Detiene la animación en una capa específica usando `stopAction()`.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Layer | Int | `0` | Capa de animación a detener (0–7) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
self.own.stopAction(0)
```

## Uso típico

### Detener animación de disparo

```
[On State Exit: FSM ID="weapon", State="firing"]
    └── On Exit ──► [Stop Action: Layer=1]
```

### Detener todas las capas al pausar

```
[On Message: Subject="game_paused"]
    → [Stop Action: Layer=0]
    → [Stop Action: Layer=1]
    → [Stop Action: Layer=2]
```

## Notas

- `stopAction(layer)` congela el objeto en el frame actual de esa capa.
- Para continuar la animación desde donde se detuvo, no existe un `resumeAction` — debes usar `playAction` con el frame actual como punto de inicio.
- Detener una capa inactiva no produce error.
