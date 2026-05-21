# Enemy AI

**Tipo:** Exec (In → Out)  
**Categoría:** AI

Nodo prefab todo-en-uno para enemigos. Encapsula detección, patrulla, persecución, cobertura, ataque, huida y muerte en un solo nodo. Ideal para prototipos o enemigos de media complejidad sin necesidad de configurar el sistema modular.

## Secciones de propiedades

### Básico

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Player | Enum | *(objetos de escena)* | Objeto del jugador |
| Physics | Enum | `Character` | Física del enemigo |
| Faction | String | `"enemy"` | Grupo de facción para compartir alertas |

### Stats / Dificultad

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Max HP | Float (≥1) | `100.0` | HP base |
| HP × | Float (≥0.01) | `1.0` | Multiplicador de HP |
| Damage × | Float (≥0.01) | `1.0` | Multiplicador de daño |
| Speed × | Float (≥0.01) | `1.0` | Multiplicador de velocidades |

El HP final = `Max HP × HP ×`. El daño y velocidades también se escalan al compilar.

### Detección

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Vision Radius | Float (≥0.1) | `12.0` | Rango máximo de visión |
| Vision Angle | Float (1–360) | `90.0` | Apertura total del cono de visión |
| Aggro Radius | Float (≥0.0) | `2.5` | Radio de detección automática sin LOS |
| Lose Target (s) | Float (≥0.1) | `3.0` | Segundos hasta perder el rastro al jugador |

### Movimiento

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Patrol Speed | Float (≥0.0) | `2.0` | Velocidad de patrulla |
| Chase Speed | Float (≥0.0) | `4.0` | Velocidad de persecución |
| Flee Speed | Float (≥0.0) | `5.0` | Velocidad de huida |
| Rotation Lerp | Float (≥0.001) | `0.1` | Suavizado de rotación |
| Move Smoothing | Float (0.01–1.0) | `0.12` | Suavizado de movimiento |
| Obstacle Avoidance | Bool | `True` | Raycast frontal para esquivar obstáculos |

### Waypoints de Patrulla

| Propiedad | Descripción |
|-----------|-------------|
| WP 1 … WP 6 | Objetos de waypoint en orden de visita |
| Mode | `Loop` (circular) o `Ping Pong` (ida y vuelta) |

### Combate

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Attack Type | Enum | `Melee` | Melee o Ranged |
| Attack Range | Float (≥0.1) | `2.0` | Distancia de ataque |
| Cooldown (s) | Float (≥0.05) | `1.5` | Segundos entre ataques |
| Damage | Float (≥0.0) | `10.0` | Daño por ataque |
| Projectile | Enum | *(objetos de escena)* | Objeto a addObject() en ataque Ranged |
| Strafe | Bool | `True` | Moverse lateralmente al perseguir |

### Cobertura

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Cover 1 … 4 | Enum | *(objetos de escena)* | Objetos de cobertura |
| Take Cover at HP % | Float (0–1) | `0.5` | Fracción de HP para entrar en cobertura |
| Select By | Enum | `Nearest` | Criterio de selección (Nearest / Farthest / Best Blocking) |
| Peek Duration (s) | Float (≥0.1) | `2.0` | Segundos en cobertura antes de asomarse a atacar |

### Thresholds

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Flee at HP % | Float (0–1) | `0.2` | Fracción de HP para huir |
| Stagger (s) | Float (≥0.0) | `0.3` | Duración del stagger al recibir daño |
| Knockback Force | Float (≥0.0) | `5.0` | Fuerza de retroceso al recibir daño |

### Group Behavior

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Alert Others | Bool | `True` | Difundir posición del jugador a enemigos de la misma facción |

### Audio

| Propiedad | Descripción |
|-----------|-------------|
| Alert Sound | Sonido al avistar al jugador |
| Attack Sound | Sonido al atacar |
| Death Sound | Sonido al morir |

### Animaciones

| Propiedad | Descripción |
|-----------|-------------|
| Idle | Nombre de la acción en reposo |
| Walk | Nombre de la acción de movimiento |
| Attack | Nombre de la acción de ataque |
| Die | Nombre de la acción de muerte |
| Layer | Capa de animación de Range |

### Muerte / Limpieza

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Death Type | Enum | `Disappear` | `Disappear` / `Stay` / `Sink` |
| Sink Speed | Float (≥0.01) | `0.5` | Velocidad de hundimiento si Sink |
| Drop Object | Enum | *(objetos de escena)* | Objeto a spawnear al morir |
| Cleanup Delay (s) | Float (≥0.0) | `3.0` | Segundos antes de ejecutar la limpieza |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Estados del prefab

```
idle → patrol → alert → chase → cover ↔ attack → flee → dead
```

| Estado | Condición de entrada |
|--------|----------------------|
| idle | Sin waypoints o perdió al jugador |
| patrol | Con waypoints, sin jugador visible |
| alert | Recibió alerta de facción (sin LOS) |
| chase | Jugador visible, fuera de rango de ataque |
| cover | HP < cover_threshold y tiene objetos de cobertura |
| attack | En rango de ataque; tras peek en cobertura |
| flee | HP < flee_threshold |
| dead | HP ≤ 0 |

## Canal de daño

**Enemy AI usa su propio canal**, distinto al del sistema modular:

```python
# Para dañar a este enemigo:
Range.logic.globalDict['_ai_dmg_' + enemy.name] = cantidad

# Con knockback:
Range.logic.globalDict['_ai_dmg_' + enemy.name] = cantidad
Range.logic.globalDict['_ai_dmg_src_' + enemy.name] = list(fuente.worldPosition)
```

> No usar `_dmg_<nombre>` — ese canal es del Damage Receiver del sistema modular.

## Variables de escena publicadas (BGE properties)

| Propiedad | Tipo | Descripción |
|-----------|------|-------------|
| `ai_state` | String | Estado actual (para animaciones nativas) |
| `ai_hp` | Float | HP actual |
| `ai_attacking` | Bool | True el frame en que ataca |

## Comportamiento del grupo (Alert Others)

Cuando el enemigo ve al jugador y `Alert Others = True`, escribe en:
```python
globalDict['_ai_alert_<faction>'] = [x, y, z]
```

Otros Enemy AI con la misma `Faction` y en estado idle leerán esta posición y cambiarán a `alert`.

## Uso típico

### Enemigo básico sin waypoints

```
Player: Player
Physics: Character
Max HP: 80.0
Vision Radius: 12.0
Attack Type: Melee
Damage: 15.0
Cooldown: 1.5
Flee at HP %: 0.2
Death Type: Disappear
Idle: "Idle"
Walk: "Walk"
Die: "Die"
```

### Soldado con cobertura y waypoints de patrulla

```
Player: Player
WP 1: Patrol_A
WP 2: Patrol_B
Mode: Ping Pong
Cover 1: Cover_Box
Take Cover at HP %: 0.5
Peek Duration: 2.5
Attack Type: Ranged
Projectile: Bullet_Prefab
```

## Comparación con el sistema modular

| Enemy AI (prefab) | Sistema Modular |
|------------------|-----------------|
| Un solo nodo | 10–11 nodos |
| Configuración directa | Requiere conectar nodos |
| Canal `_ai_dmg_*` | Canal `_dmg_*` |
| Estados hardcodeados | Extensible con BTCondition/Custom |
| Ideal para prototipos | Ideal para producción |

## Notas

- Todos los multiplicadores (HP ×, Damage ×, Speed ×) se aplican **en compilación**, no en runtime. Cambiarlos requiere recompilar el componente.
- Obstacle Avoidance lanza un raycast frontal de 1.5 unidades cada frame — deshabilitar en enemigos simples para ahorrar CPU.
- Si `Faction` está vacío, el nodo usa `"enemy"` por defecto.
- Death Type `Stay` deja el cuerpo en escena pero lo detiene (no hace endObject). `Disappear` llama `endObject()` tras el delay. `Sink` lo hunde en Y negativo antes de desaparecer.
