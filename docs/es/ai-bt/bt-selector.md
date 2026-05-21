# BT Selector

**Tipo:** Branch (In → Success / Failure / Out)  
**Categoría:** AI_BT  
**Lógica:** OR

Lee `_bt_last_result` del nodo anterior y ramifica la ejecución: si el resultado fue `SUCCESS` va por la rama **Success**; cualquier otro valor (`FAILURE` o `RUNNING`) va por **Failure**.

Equivale al nodo Selector clásico de BT: *"intenta el primer hijo; si falla, intenta el siguiente"*. En RNC, el "siguiente hijo" es literalmente la siguiente rama conectada.

## Sockets

| Socket | Dirección | Tipo | Descripción |
|--------|-----------|------|-------------|
| In | Entrada | Exec | |
| Success | Salida | Exec | Ejecuta si `_bt_last_result == 'SUCCESS'` |
| Failure | Salida | Exec | Ejecuta si `_bt_last_result != 'SUCCESS'` |
| Out | Salida | Exec | Siempre ejecuta (después de Success o Failure) |

## Tabla de verdad

| `_bt_last_result` | Rama activada |
|-------------------|---------------|
| `'SUCCESS'` | Success |
| `'FAILURE'` | Failure |
| `'RUNNING'` | Failure |
| *(no definido)* | Failure (default `'FAILURE'`) |

## Variable interna

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `_bt_selector_result` | string | Copia del resultado leído (para debug) |

## Uso típico

```
[BT Move To: target] ──► [BT Selector]
                                ├── Success ──► [BT Play Animation: "attack"]
                                └── Failure ──► [BT Wait: 2.0s]
```

En este ejemplo, si el agente llega al objetivo (`SUCCESS`), ataca. Si no puede moverse (`FAILURE`) o sigue en camino (`RUNNING`), espera.

## Patrón OR multi-opción

Para implementar "intenta A, si no B, si no C", encadena Selectors:

```
[BT Condition: enemy_visible]
    ├── True ──► [BT Move To: enemy] → [BT Selector]
    │                                       ├── Success ──► [Attack]
    │                                       └── Failure ──►
    │                                                       [BT Move To: cover] → [BT Selector]
    │                                                           ├── Success ──► [Wait in cover]
    │                                                           └── Failure ──► [BT Wait: idle]
    └── False ──► [Patrol]
```

## Notas

- El socket `Out` siempre se ejecuta, independientemente del resultado. Úsalo para lógica que debe ocurrir en todos los casos (limpiar estado, debug).
- El nodo no modifica `_bt_last_result` — lo conserva para que el siguiente nodo también pueda leerlo si es necesario.
