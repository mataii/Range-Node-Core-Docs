# Inventory Set Slot

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

Modifica un slot del inventario en tiempo de ejecución. Úsalo para pickups de armas, drops, o cualquier cambio dinámico al inventario sin necesidad de recompilar el grafo.

## Propiedades

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| Slot | Int (0–9) | `0` | Índice del slot a modificar (anulado por socket) |
| Enable | Bool | `True` | `True` = dar arma, `False` = quitar arma |
| Type | String | `"NONE"` | Tipo de arma (ej: `PISTOL`, `RIFLE`, `KNIFE`) |
| Melee | Bool | `False` | Si está activo, ignora todas las propiedades de munición |
| Ammo | Int | `30` | Balas iniciales en el cargador |
| Mag Size | Int | `30` | Capacidad máxima del cargador |
| Magazines | Int | `3` | Cargadores de reserva iniciales |
| Max Mags | Int | `5` | Máximo de cargadores de reserva |

## Sockets

| Socket | Dirección | Tipo | Descripción |
|--------|-----------|------|-------------|
| In | Entrada | Exec | |
| Slot | Entrada | Data | Índice del slot (anula la propiedad Slot) |
| Out | Salida | Exec | |

## Comportamiento

- Si `Enable = True`: el slot recibe los valores configurados.
- Si `Enable = False`: el slot se vacía (`exists = False`, tipo `"NONE"`, munición a 0).
- Si el índice está fuera del rango de `_inv_slots`, la operación se omite silenciosamente.

## Uso típico

```
[Trigger: Pickup Pistol] → [Inventory Set Slot: Slot=0, Type="PISTOL", Enable=True] → [Out]
[Trigger: Drop Weapon]   → [Inventory Set Slot: Enable=False] → [Out]
```

!!! warning "Requiere Inventory Manager en el mismo objeto"
    Modifica directamente `_inv_slots`. Sin Inventory Manager, la lista no existe y el nodo no hace nada.
