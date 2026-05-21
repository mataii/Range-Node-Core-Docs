# Weapon Recoil

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

Acumula retroceso por cada disparo y lo recupera gradualmente. Publica valores de pitch y yaw para que los nodos de cámara o viewmodel apliquen el efecto visual. **No rota nada directamente** — solo publica los datos.

## Propiedades

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| Pitch | Float | `3.0` | Retroceso vertical por disparo (grados) |
| Max Pitch | Float | `15.0` | Pitch acumulado máximo (grados) |
| Recovery Speed | Float | `8.0` | Velocidad de recuperación (grados/s) |
| Random Yaw | Bool | `True` | Retroceso horizontal aleatorio por disparo |
| Yaw Spread | Float | `1.0` | Máximo yaw aleatorio por disparo (grados) |
| Max Yaw | Float | `5.0` | Yaw acumulado máximo en cada dirección (grados) |
| Yaw Pattern | Bool | `False` | Usa un patrón determinista de yaw |
| Pattern | String | `"0.1,0.2,-0.1,0.3,-0.2"` | Valores de yaw separados por comas (grados), cíclicos |
| Reset Delay | Float | `0.0` | Segundos sin disparar antes de reiniciar el índice del patrón (0 = desactivado) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables publicadas

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `weapon_recoil_pitch` | float | Pitch acumulado (grados) |
| `weapon_recoil_yaw` | float | Yaw acumulado (grados) |
| `weapon_recoil_offset` | float | `abs(pitch) + abs(yaw)` — magnitud total del retroceso |
| `weapon_recoil_amount` | float | Alias de `weapon_recoil_offset` |
| `weapon_recoil_active` | bool | `True` cuando el retroceso > 0.001 |

## Modos de yaw

| Modo | Descripción |
|------|-------------|
| Sin yaw | Solo retroceso vertical |
| Random Yaw | Valor aleatorio entre `±yaw_spread` por disparo |
| Yaw Pattern | Secuencia determinista cíclica (mismo patrón cada cargador) |

## Integración con Weapon Balancer

Escala el retroceso por `wb_recoil` (publicado por Weapon Balancer). Si no está presente, el multiplicador es 1.0.

## Integración con Weapon ADS

El pitch y yaw publicados son datos crudos. Si quieres que el ADS reduzca el retroceso visualmente, multiplica `weapon_recoil_pitch/yaw` por `weapon_ads_recoil_mult` en el nodo que aplique la rotación de cámara.

## Posición en el grafo

```
[Weapon Fire Executor] → [Weapon Recoil] → (consumidor de cámara / viewmodel)
```
