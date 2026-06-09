# BT Play Animation

**Tipo:** Exec (In → Out)  
**Categoría:** AI_BT  
**Tarea**

Reproduce una acción de animación en el objeto. Devuelve `RUNNING` mientras la animación se reproduce, `SUCCESS` cuando alcanza el frame final, y `FAILURE` si no hay ningún action configurado.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Action | String | `""` | Nombre de la acción BGE a reproducir |
| Start | Int (≥0) | `0` | Frame de inicio |
| End | Int (≥1) | `60` | Frame de fin |
| Layer | Int (≥0) | `1` | Capa de animación de Range |
| Blend In | Int (≥0) | `4` | Frames de blend-in |
| Loop | Bool | `False` | Reproduce en bucle indefinido (siempre `RUNNING`) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Resultados

| Condición | `_bt_last_result` |
|-----------|-------------------|
| `Action` vacío | `'FAILURE'` (inmediato) |
| Animación en reproducción | `'RUNNING'` |
| Frame actual ≥ End − 1 | `'SUCCESS'` (reinicia flag interno) |
| `Loop = True` | Siempre `'RUNNING'` |

## Comportamiento

En el primer frame en que ejecuta:

1. Llama a `self.own.playAction(action, start, end, layer, priority=0, blendin, play_mode)`.
2. Marca `_btpa_<sid> = True` para no re-lanzar la animación.

Los frames siguientes solo consultan `self.own.getActionFrame(layer)` hasta que llegue a `end - 1`.

Al terminar, `_btpa_<sid>` vuelve a `False` para permitir la siguiente ejecución.

## Uso típico

### Animación de ataque antes de aplicar daño

```
[BT Condition: in_attack_range]
    └── True ──► [BT Play Animation: "Attack"] → [BT Selector]
                                                      ├── Success ──► [BT Custom Task: apply_damage]
                                                      └── Failure ──► (RUNNING, espera)
```

### Animación de muerte (una sola vez)

```
[BT Condition: hp <= 0]
    └── True ──► [BT Play Animation: "Death", Loop=False] → [BT Sequence]
                                                                  ├── Continue ──► [BT Custom Task: end_object]
                                                                  └── Abort ──► (sigue muriendo)
```

### Animación de idle en bucle

```
Loop = True
Action = "Idle"
```

Devuelve siempre `RUNNING` — úsala en la rama de fallback de un Selector.

## Notas

- `Loop = True` impide que el nodo devuelva `SUCCESS`. Si necesitas terminar el loop externamente, usa **BTCustomTask** con `stopAction()`.
- La animación se lanza **una sola vez** aunque el nodo se ejecute múltiples frames — el flag interno evita re-lanzar hasta que termine.
- Si otro nodo en una capa diferente interrumpe la animación, el detector de frame puede no funcionar correctamente. Usa capas distintas para animaciones concurrentes.
