# Play Action

**Tipo:** Acción  
**Categoría:** ANIMATION

Reproduce una acción de animación en el objeto usando `playAction()` de Range Game Engine.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Action | String | `"action_name"` | Nombre del action de Blender a reproducir |
| Start Frame | Int | `1` | Frame de inicio |
| End Frame | Int | `60` | Frame final |
| Layer | Int | `0` | Capa de animación (0–7) |
| Priority | Int | `0` | Prioridad de la capa (menor número = mayor prioridad) |
| Blend In | Float | `0.0` | Frames de transición entre animaciones |
| Play Mode | Enum | `PLAY` | Ver modos en el índice de categoría |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
self.own.playAction('idle', 1, 60, layer=0, priority=0, blendin=0.0, play_mode=0)
```

## Uso típico

### Animación idle en estado de espera

```
[On State Enter: FSM ID="player", State="idle"]
    └── On Enter ──► [Play Action:
                          Action = "player_idle"
                          Start Frame = 1
                          End Frame = 80
                          Play Mode = LOOP]
```

### Animación de disparo (capa separada)

```
[Weapon Fire Executor] → [Play Action:
                              Action = "arms_shoot"
                              Layer = 1
                              Blend In = 2.0]
```

### Animación de muerte (una vez)

```
[On State Enter: FSM ID="player", State="dead"]
    └── On Enter ──► [Play Action:
                          Action = "player_die"
                          Play Mode = PLAY]
```

## Notas

- Cada capa de animación reproduce su acción de forma independiente. Las capas superiores pueden enmascarar las inferiores dependiendo de la configuración del armature.
- `Blend In = 0.0` es un corte inmediato. Valores entre 3–10 son suaves para la mayoría de transiciones.
- Los nombres de actions deben coincidir exactamente con los del archivo blend.
