# Categoría AI_BT — Behavior Trees

La categoría **AI_BT** implementa un sistema de **Árbol de Comportamiento** (Behavior Tree) para definir la lógica de toma de decisiones de agentes de IA. En lugar de código imperativo, el comportamiento se describe como un grafo de nodos con tres posibles resultados: `SUCCESS`, `FAILURE` y `RUNNING`.

## Conceptos fundamentales

### Los tres estados de resultado

Cada nodo BT escribe en `self._bt_last_result` al terminar su ejecución:

| Estado | Significado |
|--------|-------------|
| `SUCCESS` | La tarea se completó exitosamente |
| `FAILURE` | La tarea no pudo completarse |
| `RUNNING` | La tarea está en progreso (continúa el próximo frame) |

Este valor es leído por los nodos compuestos (Selector, Sequence) que vienen inmediatamente después en el grafo.

### El Blackboard

El **blackboard** es un diccionario Python compartido por todos los nodos BT del mismo objeto:

```python
self._bt_bb  # dict{} — persiste entre frames
```

Se usa para comunicar datos entre nodos sin conectar sockets: posición del enemigo, si hay un objetivo visible, estado de patrulla, etc.

```python
# Escribir (BTSetBlackboard o BTCustomTask):
self._bt_bb['enemy_visible'] = True
self._bt_bb['target_pos']    = enemy.worldPosition.copy()

# Leer (BTCondition):
val = self._bt_bb.get('enemy_visible', False)
```

### Modelo de ejecución en RNC

A diferencia de los BT clásicos que recorren el árbol entero en un frame, **RNC ejecuta el grafo linealmente cada frame** desde `OnUpdate`. Cada nodo:

1. Ejecuta su lógica.
2. Escribe `_bt_last_result`.
3. El siguiente nodo en el grafo lo lee y reacciona.

Esto significa que **los resultados RUNNING se propagan al siguiente frame automáticamente** — el grafo se re-ejecuta desde el inicio cada frame y los nodos con estado interno (BTWait, BTMoveTo) detectan que ya están activos y actualizan su progreso.

## Grupos de nodos

### Compuestos
Los compuestos leen `_bt_last_result` y ramifican la ejecución:

| Nodo | Tipo | Lógica |
|------|------|--------|
| [BT Selector](bt-selector.md) | Branch | OR — `SUCCESS` → rama éxito, si no → rama fallo |
| [BT Sequence](bt-sequence.md) | Branch | AND — `!= FAILURE` → continuar, `FAILURE` → abortar |
| [BT Simple Parallel](bt-simple-parallel.md) | Exec | Ejecuta acción de fondo cada frame, siempre `RUNNING` |

### Decoradores
Los decoradores modifican el resultado del nodo anterior:

| Nodo | Tipo | Efecto |
|------|------|--------|
| [BT Inverter](bt-inverter.md) | Exec | Invierte `SUCCESS` ↔ `FAILURE` |
| [BT Repeater](bt-repeater.md) | Exec | Acumula N éxitos antes de propagar `SUCCESS` |
| [BT Condition](bt-condition.md) | Branch | Lee blackboard, evalúa condición, ramifica |

### Tareas
Las tareas realizan trabajo y reportan su resultado:

| Nodo | Tipo | Resultado |
|------|------|-----------|
| [BT Wait](bt-wait.md) | Exec | `RUNNING` mientras espera, `SUCCESS` al terminar |
| [BT Move To](bt-move-to.md) | Exec | `RUNNING` en tránsito, `SUCCESS` al llegar, `FAILURE` sin objetivo |
| [BT Set Blackboard](bt-set-blackboard.md) | Exec | Escribe en blackboard, siempre `SUCCESS` |
| [BT Play Animation](bt-play-animation.md) | Exec | `RUNNING` mientras reproduce, `SUCCESS` al terminar |
| [BT Custom Task](bt-custom-task.md) | Exec | Código Python libre — debe setear `_bt_last_result` |
| [BT Service](bt-service.md) | Exec | Ejecuta código cada N segundos, siempre `RUNNING` |

## Patrón de grafo típico

```
[OnUpdate]
    │
    ▼
[BT Condition: enemy_visible]
    ├── True ──► [BT Move To: enemy] ──► [BT Selector]
    │                                          ├── Success ──► [BT Play Animation: attack]
    │                                          └── Failure ──► [BT Wait: 0.5s]
    │
    └── False ──► [BT Set Blackboard: mode="patrol"] ──► [BT Move To: waypoint]
```

## Regla de oro

> **Un nodo Task siempre debe preceder al compuesto que lee su resultado.**  
> El orden en el grafo determina el orden de ejecución dentro del mismo frame.

```
✓ [BTWait] → [BTSelector]    ← Selector lee el resultado de Wait
✗ [BTSelector] → [BTWait]    ← Selector lee _bt_last_result de un frame anterior
```
