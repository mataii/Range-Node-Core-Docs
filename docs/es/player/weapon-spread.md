# Weapon Spread

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

Gestiona el cono de dispersión balística del arma. Acumula bloom por disparos y lo recupera gradualmente. Publica offsets de pitch/yaw por frame de disparo que **Weapon Hitscan** y **Weapon Projectile** consumen para desviar el rayo o el proyectil.

## Propiedades

### Dispersión base

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| Base Spread | Float | `1.0` | Cono mínimo de inaccuracidad (grados) |
| Max Spread | Float | `8.0` | Máximo bloom acumulado (grados) |
| Per Shot | Float | `2.0` | Bloom añadido por disparo (grados) |
| Recovery Speed | Float | `6.0` | Velocidad de recuperación hacia Base Spread (grados/s) |

### Penalizaciones

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| Moving Penalty | Bool | `True` | Aumenta la dispersión al moverse |
| Move Multiplier | Float | `2.0` | Multiplicador de dispersión en movimiento |
| Airborne Penalty | Bool | `True` | Aumenta la dispersión en el aire |
| Air Multiplier | Float | `3.0` | Multiplicador de dispersión en el aire |
| Crouch Bonus | Bool | `False` | Reduce la dispersión al agacharse |
| Crouch Multiplier | Float (0.1–1) | `0.6` | Multiplicador de dispersión agachado (< 1 = más preciso) |
| ADS Bonus | Bool | `True` | Aplica `weapon_ads_spread_mult` de Weapon ADS |

### Acoplamiento con Recoil

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| Recoil Coupling | Bool | `False` | Añade una fracción del recoil al bloom por disparo |
| Recoil Scale | Float | `0.5` | Fracción de `weapon_recoil_offset` añadida al bloom |

### Patrón determinista

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| Pattern Spread | Bool | `False` | Usa una lista de magnitudes fijas en lugar del bloom |
| Pattern | String | `"1.0,2.0,1.5,2.5,1.0"` | Valores de cono separados por comas (grados), cíclicos |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables publicadas

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `weapon_spread_pitch` | float | Desviación vertical del disparo (radianes). Distinto de 0 solo en frames de disparo. |
| `weapon_spread_yaw` | float | Desviación horizontal del disparo (radianes). Distinto de 0 solo en frames de disparo. |
| `weapon_spread` | float | Cono efectivo actual (radianes). Útil para HUD. |
| `weapon_spread_multiplier` | float | Multiplicador total aplicado al cono base |
| `weapon_spread_active` | bool | `True` cuando el bloom > Base Spread |
| `weapon_accuracy` | float (0–1) | `1.0` = máxima precisión, `0.0` = máximo bloom |

## Posición en el grafo

```
[Weapon Fire Executor] → [Weapon Spread] → [Hit Scan Weapon]
                                         → [Weapon Projectile]
```
