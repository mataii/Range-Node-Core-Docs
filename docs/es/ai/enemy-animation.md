# Enemy Animation

**Tipo:** Exec (In → Out)  
**Categoría:** AI  
**Execution Order:** 60 (después de Enemy Combat)

Selecciona y reproduce automáticamente la animación correcta según el estado del enemigo. Prioriza Death > Hit > Walk > Idle y solo cambia la acción si la animación es distinta a la actual.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Idle | String | `""` | Nombre de la acción de Range en reposo |
| Walk | String | `""` | Nombre de la acción de movimiento |
| Hit | String | `""` | Nombre de la acción de golpe recibido |
| Death | String | `""` | Nombre de la acción de muerte |
| Layer | Int (≥0) | `0` | Capa de animación de Range |
| Blend | Float (≥0) | `4.0` | Frames de blend-in al cambiar animación |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables leídas

| Variable | Fuente |
|----------|--------|
| `ai_is_dead` | Damage Receiver |
| `ai_took_damage` | Damage Receiver |
| `ai_is_moving` | Enemy Movement |

## Comportamiento

### Prioridad de selección

```
1. ai_is_dead     → Death (play_mode=0, una vez)
2. _ea_timer > 0  → Hit  (continúa durante 0.3s)
3. ai_took_damage → Hit  (activa timer de 0.3s)
4. ai_is_moving   → Walk
5. defecto        → Idle
```

La animación se cambia **solo** cuando el nombre de la acción seleccionada difiere de `self._ea_current`. Esto evita reiniciar la acción cada frame.

### Timer de hit

Al recibir daño (`ai_took_damage = True`), el timer interno `_ea_timer` se establece en 0.3 segundos. Durante ese tiempo, la animación Hit tiene prioridad sobre Walk e Idle.

## Uso típico

### Configuración básica

```
Idle:  "Idle"
Walk:  "Walk"
Hit:   "Hit"
Death: "Death"
Layer: 0
Blend: 4.0
```

```
[Enemy Movement] → [Enemy Animation]
```

### Con capa separada para hit

```
Layer: 0  (Idle/Walk/Death)
```
```
[Otro Enemy Animation con Layer=1]  (Hit en capa superior)
```

## Notas

- Si un campo de animación está vacío (`""`), esa animación no se intenta reproducir — el nodo pasa al siguiente en la jerarquía de prioridad.
- `play_mode=0` en Death significa reproducción única (no loop). El resto de animaciones usan `play_mode=0` también pero se llaman de nuevo si el estado cambia, lo que produce loop implícito.
- El blend-in se aplica al inicio de cada transición, no entre frames de la misma animación.
- Enemy Animation no gestiona el estado del SM directamente — solo lee `ai_is_dead`, `ai_took_damage` y `ai_is_moving`. Para animaciones por estado específico (ej. animación de ataque), usa un nodo adicional que lea `ai_attack_requested`.
