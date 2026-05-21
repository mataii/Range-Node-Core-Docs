# Line Of Sight

**Tipo:** Branch (Visible / Hidden)  
**Categoría:** AI

Verifica si el objeto propietario tiene línea de visión directa hacia un objetivo mediante raycast. Bifurca la ejecución según el resultado.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Target | Enum | *(objetos de escena)* | Objeto de referencia |
| Max Distance | Float (≥0.1) | `15.0` | Distancia máxima de detección |
| Obstacle Filter | String | `""` | Propiedad BGE que bloquea el raycast (vacío = cualquier objeto) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Visible | Salida | Exec (target a la vista) |
| Hidden | Salida | Exec (target oculto u fuera de rango) |

## Comportamiento

Cada frame:

1. Busca el objeto Target en escena.
2. Si no existe: ejecuta `Hidden`.
3. Calcula la distancia al target.
4. Lanza un raycast desde el objeto propietario hasta la posición del target:
   ```python
   hit, _, _ = self.own.rayCast(
       target.worldPosition, self.own.worldPosition,
       max_distance, prop_filter, 1, 1
   )
   ```
5. Es visible si: `hit == target` y `distancia ≤ max_distance`.

### Obstacle Filter

Si `Obstacle Filter` está vacío, **cualquier objeto** entre el propietario y el target bloquea la línea de visión. Si tiene valor (ej. `"wall"`), solo objetos con esa propiedad BGE la bloquean. Usa esto para que el agua, triggers o zonas transparentes no interrumpan la detección.

## Uso típico

### Detección simple

```
[Line Of Sight: Player, Max Distance=15.0]
    ├── Visible ──► [Seek: Player]
    └── Hidden  ──► [Wander]
```

### Distance Check + Line Of Sight (optimización)

El raycast es costoso — limitar la distancia primero ahorra llamadas:

```
[Distance Check: Player, Threshold=15.0]
    ├── Near ──► [Line Of Sight: Player, Max Distance=15.0]
    │               ├── Visible ──► [Seek: Player]
    │               └── Hidden  ──► [Patrol]
    └── Far  ──► [Patrol]
```

### Filtrado de obstáculos

Solo paredes con propiedad `solid` bloquean la visión:

```
Obstacle Filter: "solid"
```

## Diferencia con Enemy Perception

| Line Of Sight | Enemy Perception |
|---------------|-----------------|
| Un raycast por frame | Configurable: cada N segundos |
| Sin FOV | FOV angular configurable |
| Sin memoria | Memoria de última posición vista |
| No publica `ai_*` | Publica todo el contrato `ai_*` |
| Standalone | Parte del sistema modular |

## Notas

- El raycast se lanza desde el origen del objeto propietario, no desde los ojos. Para enemigos con cabeza alta, el raycast puede quedar bajo los obstáculos. Usa un Empty hijo a la altura de los ojos y haz el raycast con BTCustomTask si necesitas precisión.
- El nodo hace `rayCast(target, own, distance, prop, 1, 1)` con xray=1 — el raycast penetra el objeto propietario y no se bloquea con sí mismo.
- Si el target está exactamente en el límite de `max_distance`, se considera `Hidden`.
