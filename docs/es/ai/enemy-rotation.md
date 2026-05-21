# Enemy Rotation

**Tipo:** Exec (In → Out)  
**Categoría:** AI  
**Execution Order:** 56 (después de Enemy Movement)

Rota el objeto propietario para que mire hacia `ai_target_pos`. Soporta rotación suavizada (slerp) o instantánea.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Rotation Speed | Float (≥0.1) | `8.0` | Velocidad angular de rotación |
| Smooth | Bool | `True` | Interpolar la rotación (True) o instantánea (False) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables leídas

| Variable | Fuente |
|----------|--------|
| `ai_target_pos` | Enemy Perception |

## Comportamiento

Cada frame:

1. Lee `ai_target_pos`.
2. Si es un Vector válido: calcula la dirección XY al target.
3. Calcula el ángulo de giro necesario (`atan2` del cross product con el vector frontal Y del objeto).
4. Aplica la rotación:
   - **Smooth = True:** escala el ángulo por `min(1.0, rotation_speed * deltaTime * 60)` — rotación progresiva independiente del framerate.
   - **Smooth = False:** aplica el ángulo completo instantáneamente.
5. Si no hay `ai_target_pos` (target no visible): no rota.

## Uso típico

### Enemigo que siempre mira al jugador cuando lo ve

```
[Enemy Perception] → [Enemy Movement] → [Enemy Rotation]
```

La rotación solo ocurre cuando `ai_target_pos` es válido (target visible). Al perder de vista al jugador, el enemigo sigue mirando en la última dirección girada.

### Ajustar velocidad de giro

```
Rotation Speed: 4.0   # giro lento, tanque
Rotation Speed: 15.0  # giro rápido, drone
```

El factor de escala es `rotation_speed * deltaTime * 60`, por lo que el comportamiento es consistente entre framerates.

## Notas

- El eje de rotación es siempre Z (rotación horizontal). El nodo no gestiona inclinación vertical.
- El eje frontal del objeto es `-Y` (`to_track_quat('-Y', 'Z')`). Si tu modelo mira en una dirección distinta, rota el mesh dentro del objeto o ajusta con un nodo de rotación adicional.
- Cuando `ai_target_pos` es None (target fuera de vista o muerto), Enemy Rotation no actúa — el objeto queda rotado en la última orientación conocida.
- Para rotar hacia el destino de movimiento (no hacia el target), usa un BTCustomTask con la misma lógica pero leyendo `ai_move_target`.
