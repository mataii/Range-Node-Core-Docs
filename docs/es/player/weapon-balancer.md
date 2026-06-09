# Weapon Balancer

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

Aplica multiplicadores por clase de arma a los parámetros del pipeline. Define una tabla de stats (Pistol, Rifle, Shotgun, Sniper, SMG, etc.) y los aplica automáticamente según `weapon_type`, publicando los multiplicadores `wb_*` que los demás nodos consumen.

## Propiedades

### Topes globales

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Damage Cap | Float (0.1–20) | `5.0` | Multiplicador máximo de daño |
| Recoil Cap | Float (0.1–20) | `5.0` | Multiplicador máximo de retroceso |
| Spread Cap | Float (0.1–20) | `5.0` | Multiplicador máximo de dispersión |
| Fire Rate Cap | Float (0.1–20) | `5.0` | Multiplicador máximo de cadencia |
| Fallback | String | `"rifle"` | Clase usada cuando `weapon_type` no está en la tabla |
| Debug | Bool | `False` | Imprime los multiplicadores activos en la consola |

### Modificadores globales

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| ADS Spread × | Float (0–1) | `0.60` | Spread cuando ADS activo (< 1 = más preciso) |
| ADS Recoil × | Float (0–1) | `0.80` | Recoil cuando ADS activo |
| Hipfire × | Float (≥1) | `1.30` | Penalización de spread sin apuntar |
| Move × | Float (≥1) | `1.40` | Penalización de spread en movimiento |

### Stats por clase (D=Damage, R=Recoil, S=Spread, F=Fire Rate)

| Clase | D | R | S | F |
|-------|---|---|---|---|
| Pistol | 0.75 | 0.60 | 1.10 | 1.40 |
| Rifle | 1.00 | 1.00 | 1.00 | 1.00 |
| Shotgun | 1.40 | 1.70 | 2.50 | 0.55 |
| Sniper | 2.50 | 2.00 | 0.30 | 0.20 |
| SMG | 0.50 | 0.70 | 1.30 | 2.00 |
| LMG | 0.90 | 1.40 | 1.60 | 1.80 |
| Melee | 1.00 | 0.00 | 0.00 | 1.00 |

*(Todos los valores son editables por defecto)*

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables publicadas (`wb_*`)

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `wb_damage` | float | Multiplicador de daño |
| `wb_recoil` | float | Multiplicador de retroceso |
| `wb_spread` | float | Multiplicador de dispersión |
| `wb_fire_rate` | float | Multiplicador de cadencia |
| `wb_class` | string | Clase de arma activa |

## Posición en el grafo

```
[Weapon State Publisher] → [Weapon Balancer] → [Weapon Reload] → [Weapon Fire Executor] → ...
```

Debe ir **antes** de Fire Executor y de los nodos de daño para que los multiplicadores estén disponibles.
