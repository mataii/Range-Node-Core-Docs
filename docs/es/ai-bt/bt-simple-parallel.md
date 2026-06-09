# BT Simple Parallel

**Tipo:** Exec (In → Out)  
**Categoría:** AI_BT  
**Resultado:** siempre `RUNNING`

Ejecuta una **acción de fondo** en cada frame (escribiendo un valor al blackboard) mientras la ejecución principal continúa por `Out`. Permite simular paralelismo: una tarea que ocurre continuamente mientras el árbol procesa otras ramas.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| BB Key | String | `""` | Clave del blackboard a actualizar cada frame |
| BB Value | String | `"True"` | Expresión Python evaluada cada frame para el valor |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Comportamiento

Cada frame que el nodo ejecuta:

1. Evalúa `BB Value` como expresión Python.
2. Escribe el resultado en `self._bt_bb[BB Key]`.
3. Fija `_bt_last_result = 'RUNNING'`.
4. Continúa por `Out`.

Si `BB Key` está vacío, el paso 2 se omite (`pass`).

## Casos de uso

### Actualizar posición del objetivo continuamente

```
BB Key:   target_pos
BB Value: self.own.scene.objects.get('Player').worldPosition.copy() if self.own.scene.objects.get('Player') else None
```

El blackboard siempre tendrá la posición actualizada del jugador, disponible para BTCondition o BTMoveTo en el mismo frame.

### Marcar estado activo

```
BB Key:   is_chasing
BB Value: True
```

Mientras este nodo esté en el árbol activo, `is_chasing` permanece `True`.

### Calcular distancia al objetivo

```
BB Key:   dist_to_player
BB Value: (self.own.worldPosition - self.own.scene.objects.get('Player', self.own).worldPosition).length
```

## Posición en el grafo

```
[OnUpdate] → [BT Simple Parallel: actualizar target_pos]
                    │
                    Out ──► [BT Condition: enemy_visible] → ...
```

Colócalo al inicio del árbol para que los datos de fondo estén frescos antes de evaluar condiciones.

## Notas

- Siempre devuelve `RUNNING` — no puede `SUCCESS` ni `FAILURE`. Si necesitas un resultado condicional, usa **BTCustomTask** o **BTService**.
- A diferencia de **BTService**, **BT Simple Parallel** ejecuta su código **cada frame**, no en intervalos.
- Errores en `BB Value` no crashean el árbol — el campo se omite silenciosamente si está vacío.
