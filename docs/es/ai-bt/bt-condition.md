# BT Condition

**Tipo:** Branch (In → True / False)  
**Categoría:** AI_BT

Lee una clave del blackboard, evalúa una expresión Python y ramifica la ejecución. También fija `_bt_last_result` según el resultado, para que un Selector o Sequence posterior pueda leerlo.

Es el nodo de **toma de decisiones** del árbol: la "pregunta" que determina qué rama se activa.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| BB Key | String | `""` | Clave del blackboard a leer. Su valor queda disponible como `val` en la condición |
| Condition | String | `"val == True"` | Expresión Python. `val` = valor del blackboard, `self` = componente |

## Sockets

| Socket | Dirección | Tipo | Descripción |
|--------|-----------|------|-------------|
| In | Entrada | Exec | |
| True | Salida | Exec | Ejecuta si la condición es verdadera |
| False | Salida | Exec | Ejecuta si la condición es falsa |

## Variables afectadas

| Variable | Valor tras ejecución |
|----------|---------------------|
| `_bt_last_result` | `'SUCCESS'` si condición es `True`, `'FAILURE'` si es `False` |

## Expresiones de ejemplo

### Comparación simple
```
BB Key:    enemy_visible
Condition: val == True
```

### Distancia umbral
```
BB Key:    dist_to_player
Condition: val is not None and val < 10.0
```

### Verificar tipo de estado
```
BB Key:    ai_state
Condition: val in ('PATROL', 'IDLE')
```

### Sin blackboard (condición directa sobre self)
```
BB Key:    (vacío)
Condition: self.own.worldPosition.z > 0.5
```

Cuando `BB Key` está vacío, `val` será `None`. Puedes ignorar `val` y escribir cualquier expresión Python que use `self`.

### Verificar propiedad BGE del objeto
```
BB Key:    (vacío)
Condition: self.own.get('hp', 100) > 20
```

## Comportamiento ante errores

Si la expresión lanza una excepción (nombre de variable incorrecto, método no encontrado, etc.), el resultado es `False` (→ rama False, `_bt_last_result = 'FAILURE'`). No hay crash.

## Posición en el grafo

```
[OnUpdate] → [BT Simple Parallel: actualizar enemy_visible]
                    │
                    Out ──► [BT Condition: enemy_visible]
                                  ├── True ──► [BT Move To: enemy]
                                  └── False ──► [BT Move To: patrol_point]
```

## Notas

- BTCondition **no es solo un decorador** — es también un nodo de flujo (como Branch en FLOW). Sus salidas True/False pueden conectar a ramas completamente distintas del árbol.
- A diferencia de BTInverter, BTCondition **define** el resultado en lugar de transformarlo.
- Si necesitas evaluar múltiples condiciones en AND/OR, usa una expresión compuesta: `val and self.own.get('hp', 0) > 0`.
