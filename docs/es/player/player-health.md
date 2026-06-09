# Player Health

**Tipo:** Branch (In → Alive / Dead)  
**Categoría:** PLAYER

Recibe daño cada frame a través de un socket de datos, descuenta HP y ramifica la ejecución entre `Alive` y `Dead`. Opcionalmente regenera HP pasado un tiempo de espera tras el último daño.

Al iniciarse, intenta cargar HP guardado desde la propiedad BGE `_sl_player_hp` del objeto (integración con **Save Load System**).

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Max HP | Float | `100.0` | Puntos de vida máximos |
| Regen | Bool | `False` | Activa la regeneración automática |
| Rate | Float | `5.0` | HP regenerados por segundo |
| Delay | Float | `3.0` | Segundos sin recibir daño antes de que empiece la regeneración |

## Sockets

| Socket | Dirección | Tipo | Descripción |
|--------|-----------|------|-------------|
| In | Entrada | Exec | |
| Damage | Entrada | Data | Puntos de daño a aplicar este frame (0 si no está conectado) |
| Alive | Salida | Exec | Se ejecuta mientras HP > 0 |
| Dead | Salida | Exec | Se ejecuta cuando HP ≤ 0 |

## Flujo de ejecución

```
frame
  │
  ├── Si Damage > 0 y HP > 0:
  │       HP = max(0, HP - Damage)
  │       regen_timer = Delay
  │
  ├── Si Regen activo:
  │       regen_timer -= dt
  │       Si regen_timer ≤ 0 y HP < Max HP:
  │           HP = min(Max HP, HP + Rate * dt)
  │
  └── HP > 0? → Alive : Dead
```

## Estado interno (`_ph_*`)

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `_ph_hp` | float | HP actual |
| `_ph_max_hp` | float | HP máximo |
| `_ph_regen_timer` | float | Tiempo restante antes de que empiece la regeneración |

Leer estos valores con **Player Health State**.

## Integración con guardado

En el primer frame, si existe la propiedad BGE `_sl_player_hp` en el objeto, el HP se inicializa con ese valor en lugar de `Max HP`. Esto permite restaurar el HP al cargar una partida con **Save Load System**.

## Grafo de ejemplo

```
[Damage Data] ──┐
                ▼
[OnUpdate] → [Player Health]
                ├── Alive ──► [Player Movement] → [...]
                └── Dead  ──► [Play Anim Death] → [End Game]
```
