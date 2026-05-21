# Get Property

**Tipo:** Dato  
**Categoría:** DATA

Lee una propiedad BGE del objeto que ejecuta el componente. Devuelve el valor o `None` si no existe.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Property | String | `"prop"` | Nombre de la propiedad BGE |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Value | Salida | Dato (cualquier tipo) |

## Código generado

```python
self.own.get('score', None)
```

## Uso típico

### Leer puntaje para mostrar en HUD

```
[On Update] → [Set Text: Text = {Get Property: prop="score"}]
```

### Condición sobre propiedad

```
[On Update] → [BT Condition: {Get Property: prop="health"} <= 0]
                  └── True ──► [Node Broadcast: Subject="player_dead"]
```

### Pasar propiedad a otro nodo

```
[Set State: State = {Get Property: prop="target_state"}]
```

## Notas

- Usa `self.own.get(prop, None)` — si la propiedad no existe, devuelve `None` sin lanzar error.
- Para escribir, usa [Set Property](set-property.md).
- Las propiedades BGE pueden ser `int`, `float`, `bool` o `str`.
