# BT Sequence

**Tipo:** Branch (In → Continue / Abort / Out)  
**Categoría:** AI_BT  
**Lógica:** AND

Lee `_bt_last_result` del nodo anterior y ramifica: si el resultado **no fue** `FAILURE` va por **Continue**; si fue `FAILURE` va por **Abort** y fija `_bt_last_result = 'FAILURE'` para propagarlo.

Equivale al nodo Sequence clásico de BT: *"ejecuta todos los hijos en orden; si cualquiera falla, aborta toda la secuencia"*. En RNC, cada "hijo" es un nodo conectado en serie antes del Sequence.

## Sockets

| Socket | Dirección | Tipo | Descripción |
|--------|-----------|------|-------------|
| In | Entrada | Exec | |
| Continue | Salida | Exec | Ejecuta si `_bt_last_result != 'FAILURE'` |
| Abort | Salida | Exec | Ejecuta si `_bt_last_result == 'FAILURE'` |
| Out | Salida | Exec | Siempre ejecuta |

## Tabla de verdad

| `_bt_last_result` | Rama activada | `_bt_last_result` después |
|-------------------|---------------|--------------------------|
| `'SUCCESS'` | Continue | sin cambio |
| `'RUNNING'` | Continue | sin cambio |
| `'FAILURE'` | Abort | `'FAILURE'` (confirmado) |
| *(no definido)* | Continue (default `'SUCCESS'`) | sin cambio |

## Uso típico

```
[BT Condition: has_ammo] ──► [BT Sequence]
                                   ├── Continue ──► [BT Move To: enemy] → [BT Sequence]
                                   │                                            ├── Continue ──► [BT Custom Task: shoot]
                                   │                                            └── Abort ──► [BT Wait: reposition]
                                   └── Abort ──► [BT Move To: ammo_pickup]
```

En este ejemplo: si el agente tiene munición (`SUCCESS`), avanza hacia el enemigo. Si llega (`SUCCESS`), dispara. Cualquier `FAILURE` en la cadena aborta y va a la rama alternativa.

## Diferencia con Selector

| | Selector | Sequence |
|--|----------|----------|
| Continúa si | `SUCCESS` | `SUCCESS` **o** `RUNNING` |
| Aborta si | `FAILURE` o `RUNNING` | `FAILURE` |
| Filosofía | "intenta alternativas" | "todos los pasos deben cumplirse" |

## Patrón AND multi-paso

Para una secuencia de pasos que todos deben completarse:

```
[Paso 1: detectar objetivo] → [BT Sequence]
                                   ├── Continue ──► [Paso 2: acercarse] → [BT Sequence]
                                   │                                           ├── Continue ──► [Paso 3: atacar]
                                   │                                           └── Abort ──► (camino bloqueado)
                                   └── Abort ──► (sin objetivo)
```

## Notas

- `RUNNING` **no aborta** la secuencia — el paso actual continúa hasta completarse (`SUCCESS`) o fallar (`FAILURE`).
- Al abortar, el nodo fija `_bt_last_result = 'FAILURE'` explícitamente, permitiendo que un Selector padre lo detecte.
