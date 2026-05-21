# Object Position

**Tipo:** Dato  
**Categoría:** MATH

Devuelve una componente de la posición de mundo (`worldPosition`) de un objeto de la escena.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Object | String | `""` | Nombre del objeto. Vacío = `self.own` |
| Component | Enum | `X` | `X`, `Y`, `Z` |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Value | Salida | Dato (Float) |

## Código generado

```python
# Component=X
scene.objects['waypoint'].worldPosition[0]

# self.own
self.own.worldPosition[1]  # Y
```

## Uso típico

### Comparar alturas de dos objetos

```
[On Update] → [BT Condition:
                  {Object Position: Object="Player", Component=Z} >
                  {Object Position: Component=Z}]
                  └── True ──► [...]  # jugador por encima del enemigo
```

### Pasar posición a otro sistema

```
[Play Tween:
    From X = {Object Position: Object="start_marker", Component=X}
    To X   = {Object Position: Object="end_marker",   Component=X}]
```

## Notas

- Nodo de solo datos — no tiene sockets Exec.
- Devuelve `worldPosition[component_index]` directamente — no transforma por escala del objeto.
