# BT Set Blackboard

**Tipo:** Exec (In → Out)  
**Categoría:** AI_BT  
**Resultado:** siempre `SUCCESS`

Escribe un valor en el blackboard (`self._bt_bb`). Siempre devuelve `SUCCESS`. Es el mecanismo principal para pasar datos entre nodos BT sin conectar sockets.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Key | String | `"my_key"` | Clave del blackboard a escribir |
| Value | String | `"True"` | Expresión Python evaluada al momento de ejecutar |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Expresiones de Value

El campo `Value` se evalúa como código Python en el contexto del componente. `self` está disponible.

| Expresión | Resultado |
|-----------|-----------|
| `True` | booleano True |
| `42` | entero 42 |
| `'patrol'` | string "patrol" |
| `self.own.worldPosition.copy()` | posición actual del objeto |
| `self.own.scene.objects.get('Enemy').worldPosition` | posición de otro objeto |
| `self.own.get('hp', 100)` | propiedad BGE del objeto |

Si la expresión lanza una excepción, la clave queda con valor `None` (no hay crash).

## Resultados

| Condición | `_bt_last_result` |
|-----------|-------------------|
| Siempre | `'SUCCESS'` |

## Uso típico

### Marcar estado de IA

```
[BT Set Blackboard: mode = 'chase'] → [BT Move To: Player]
```

### Capturar posición del objetivo

```
[BT Custom Task: find_player] → [BT Set Blackboard: target_pos = self.own.scene.objects.get('Player').worldPosition.copy()]
```

### Resetear cooldown

```
[BT Set Blackboard: attack_cooldown = 0.0] → [BT Sequence] → ...
```

### Incrementar contador

```
Key:   patrol_index
Value: (self._bt_bb.get('patrol_index', 0) + 1) % 4
```

Hace un contador cíclico de 0 a 3 sin necesidad de código externo.

## Posición en el grafo

BTSetBlackboard generalmente precede a la acción que usará el valor escrito, o se coloca en ramas de condición para actualizar el estado cuando se toma una decisión:

```
[BT Condition: enemy_visible]
    ├── True ──► [BT Set Blackboard: last_seen_pos = self.own.scene.objects.get('Player').worldPosition.copy()]
    │                   │
    │                   Out ──► [BT Move To: Player]
    └── False ──► [BT Move To: last_seen_pos_object]
```

## Notas

- Siempre devuelve `SUCCESS`, lo que lo hace seguro en cualquier posición de una Sequence.
- El blackboard persiste entre frames — los valores escritos están disponibles en el próximo frame.
- Para leer el blackboard, usa **BTCondition** o accede directamente con `self._bt_bb.get('key', default)` en un **BTCustomTask**.
