# Player Health State

**Tipo:** Data (sin sockets exec)  
**Categoría:** PLAYER

Nodo de datos puro. Lee el estado de salud almacenado por **Player Health** en el mismo objeto.

## Propiedades

| Propiedad | Tipo | Descripción |
|-----------|------|-------------|
| Data | Enum | Valor a exponer |

### Opciones del selector Data

| Opción | Tipo | Descripción |
|--------|------|-------------|
| Current HP | float | HP actual (`_ph_hp`) |
| Max HP | float | HP máximo (`_ph_max_hp`) |
| Is Alive | bool | `True` si HP > 0 |
| Percent | float (0.0–1.0) | HP actual / HP máximo |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Value | Salida | Data |

## Uso típico

```
[Player Health State: PERCENT] ──► [UI Health Bar: Set Value]
[Player Health State: CURRENT_HP] ──► [Compare: < 20] ──► [Play Low HP Sound]
```

!!! info "Requiere Player Health en el mismo objeto"
    Sin Player Health, devuelve `100.0` / `True` / `1.0`.
