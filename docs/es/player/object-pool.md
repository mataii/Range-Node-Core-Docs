# Object Pool

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

Pre-instancia un número fijo de objetos al inicio del juego y los aparca fuera de la pantalla. Los nodos consumidores (como Weapon Projectile o Projectile System) adquieren y liberan objetos del pool sin llamar a `addObject()`/`endObject()` cada frame, reduciendo el costo de garbage collection.

## Propiedades

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| Object Name | String | `""` | Nombre del objeto plantilla inactivo en la escena |
| Pool Size | Int (1–256) | `10` | Objetos a pre-instanciar al inicio |
| Park X | Float | `-9999.0` | Posición X donde se aparcan los objetos inactivos (fuera de pantalla) |
| Park Spacing | Float | `2.0` | Separación Y entre objetos aparcados |
| Dynamic Expand | Bool | `False` | Spawnea un objeto extra cuando el pool se agota (máximo 2× Pool Size) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables publicadas

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `_pool_objects` | list | Lista de referencias a los objetos del pool |
| `pool_available` | int | Objetos libres disponibles actualmente |
| `pool_active_count` | int | Objetos actualmente en uso |

## Cómo funciona

1. En el primer frame, el nodo llama a `addObject()` por cada slot del pool y aparca los objetos en `(Park X, i * Park Spacing, 0)`.
2. Para adquirir un objeto del pool, un consumidor busca el primero con `_pool_active = False`, activa el flag y lo reposiciona.
3. Para liberar un objeto, el consumidor (Projectile System con `Use Pool = True`) desactiva `_pool_active` y devuelve el objeto a su posición de aparcamiento.

## Posición en el grafo

Debe estar **antes** de cualquier nodo que consuma el pool en el mismo frame:

```
[OnUpdate] → [Object Pool] → [Weapon Projectile: Use Pool] → ...
```

## Cuándo usar un pool

| Escenario | Tamaño recomendado |
|-----------|-------------------|
| Pistola (disparo lento) | 5–10 |
| Rifle automático | 15–20 |
| Escopeta (multi-pellet) | 20–30 |
| Múltiples enemigos disparando | 30–50+ |
