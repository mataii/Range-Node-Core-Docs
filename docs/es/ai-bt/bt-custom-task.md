# BT Custom Task

**Tipo:** Exec (In → Out)  
**Categoría:** AI_BT  
**Tarea**

Ejecuta código Python libre. Debe establecer `self._bt_last_result` a `'SUCCESS'`, `'FAILURE'` o `'RUNNING'` según el resultado de la lógica. Es la válvula de escape del sistema BT para cualquier comportamiento no cubierto por los nodos estándar.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Code | String | `"self._bt_last_result = 'SUCCESS'"` | Código Python a ejecutar cada frame |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables disponibles en `Code`

| Variable | Descripción |
|----------|-------------|
| `self` | El componente Python (`KX_PythonComponent`) |
| `self.own` | El objeto de la escena (`KX_GameObject`) |
| `self._bt_bb` | El blackboard (dict persistente) |
| `self._bt_last_result` | El resultado actual (léelo o escríbelo) |
| `Range` | Módulo de la lógica de Range Game Engine |
| `mathutils` | Módulo de matemáticas vectoriales |

## Comportamiento ante errores

Si el código lanza una excepción:

1. Se imprime el error en consola: `[BTCustomTask] Error: <excepción>`.
2. Se establece `_bt_last_result = 'FAILURE'` automáticamente.
3. La ejecución continúa por `Out` — no hay crash.

## Ejemplos de uso

### Atacar al jugador con daño al globalDict

```python
player = self.own.scene.objects.get('Player')
if player:
    dist = (player.worldPosition - self.own.worldPosition).length
    if dist < 1.5:
        key = '_dmg_' + player.name
        Range.logic.globalDict[key] = Range.logic.globalDict.get(key, 0) + 25.0
        self._bt_last_result = 'SUCCESS'
    else:
        self._bt_last_result = 'FAILURE'
else:
    self._bt_last_result = 'FAILURE'
```

### Detectar al jugador y guardarlo en blackboard

```python
player = self.own.scene.objects.get('Player')
if player:
    diff = player.worldPosition - self.own.worldPosition
    diff.z = 0.0
    if diff.length < 15.0:
        self._bt_bb['target'] = player
        self._bt_bb['dist']   = diff.length
        self._bt_last_result  = 'SUCCESS'
    else:
        self._bt_last_result  = 'FAILURE'
else:
    self._bt_last_result = 'FAILURE'
```

### Timer manual con estado persistente

```python
if not hasattr(self, '_my_timer'):
    self._my_timer = 0.0
self._my_timer += Range.logic.deltaTime()
if self._my_timer >= 3.0:
    self._my_timer = 0.0
    self._bt_last_result = 'SUCCESS'
else:
    self._bt_last_result = 'RUNNING'
```

### Terminar el objeto

```python
self.own.endObject()
self._bt_last_result = 'SUCCESS'
```

## Notas

- El campo `Code` admite una sola línea visible en el editor, pero puede contener saltos de línea (`\n`) si editas el texto desde Python o lo copias con sangría.
- No uses `exec()` dentro de BTCustomTask — ya está siendo compilado y ejecutado por el compilador de RNC.
- Si la lógica es compleja, considera crear un módulo Python externo e importarlo: `import my_ai_utils; my_ai_utils.attack(self)`.
- Siempre establece `_bt_last_result` — si el código finaliza sin hacerlo, el valor del frame anterior persiste y puede causar comportamiento impredecible.
