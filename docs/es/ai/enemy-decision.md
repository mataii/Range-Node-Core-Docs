# Enemy Decision

**Tipo:** Branch (Chase / Idle)  
**Categoría:** AI  
**Execution Order:** 51 (después de AI State Machine)

Nodo de bifurcación que lee `ai_can_see` y divide la ejecución en dos ramas: perseguir si el target es visible, o quedarse en idle si no lo es.

## Propiedades

Ninguna — el nodo lee directamente `ai_can_see` de `self`.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Chase | Salida | Exec (target visible) |
| Idle | Salida | Exec (target no visible) |

## Comportamiento

```python
if getattr(self, 'ai_can_see', False):
    # → Chase
else:
    # → Idle
```

Usa `getattr` para ser seguro si Enemy Perception no está en la escena.

## Uso típico

Enemy Decision es el nodo de distribución central del sistema modular. Se coloca **después** de AI State Machine para bifurcar los nodos de movimiento y combate:

### Pipeline básico con Enemy Decision

```
[Enemy Perception] → [Damage Receiver] → [AI State Machine]
                                               │ Out
                                       [Enemy Decision]
                                           ├── Chase ──► [Enemy Movement] → [Enemy Combat]
                                           └── Idle  ──► [Wander]
```

### Con animaciones por rama

```
[Enemy Decision]
    ├── Chase ──► [Enemy Movement]
    │                  │
    │             [Enemy Animation: Walk]
    └── Idle  ──► [Enemy Animation: Idle]
```

## Diferencia con AI State Machine

| Enemy Decision | AI State Machine |
|---------------|-----------------|
| Branch simple (visible/no visible) | Máquina de estados completa (7 estados) |
| Sin configuración | 7 propiedades ajustables |
| Para pipelines simples | Para comportamientos complejos |

## Alternativas

Para lógicas más complejas que solo visible/no-visible, lee `ai_state` directamente en un BTCustomTask:

```python
state = getattr(self, 'ai_state', 'IDLE')
if state == 'ATTACK':
    self._bt_last_result = 'SUCCESS'
elif state == 'CHASE':
    self._bt_last_result = 'RUNNING'
else:
    self._bt_last_result = 'FAILURE'
```

## Notas

- Chase/Idle son nombres sugeridos — el nodo en realidad refleja `ai_can_see`, no el estado del SM.
- Si Enemy Perception no está en el árbol, `ai_can_see` es False por defecto → siempre ejecuta Idle.
- Para reaccionar al estado completo del SM (CHASE, ATTACK, RETREAT...), usa un BTCondition que lea `self.ai_state == 'CHASE'` en lugar de Enemy Decision.
