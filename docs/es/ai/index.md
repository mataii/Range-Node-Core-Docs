# Categoría AI

La categoría **AI** contiene los nodos de inteligencia artificial de Range Node Core. Cubren desde comportamientos de movimiento primitivos hasta un sistema modular completo de percepción, estados, navegación y combate.

## Dos enfoques

### Enfoque prefab — Enemy AI

Un único nodo **Enemy AI** que encapsula toda la lógica: detección, patrulla, persecución, cobertura, ataque, huida y muerte. Ideal para prototipos rápidos o enemigos simples.

### Enfoque modular — Sistema Enemy

Once nodos especializados que se pueden combinar y sustituir de forma independiente. Cada nodo publica variables `ai_*` que los demás leen vía `getattr`. El orden de ejecución está predefinido por `execution_order`.

```
[OnUpdate]
    │
    ├── [Enemy Perception]      execution_order=40
    ├── [Damage Receiver]       execution_order=42
    ├── [AI State Machine]      execution_order=50
    ├── [Enemy Decision]        execution_order=51  (branch)
    ├── [Navigation Pathfinder] execution_order=52
    ├── [Navigation Follow Path]execution_order=54
    ├── [Enemy Movement]        execution_order=55
    ├── [Enemy Rotation]        execution_order=56
    ├── [Enemy Combat]          execution_order=58
    ├── [Enemy Animation]       execution_order=60
    └── [Enemy Hit Flash]       execution_order=62
```

### Nodos de movimiento primitivo

Seek, Flee, Patrol y Wander son nodos autónomos que no dependen del sistema modular. Se pueden usar solos o combinados con BTCondition/BT nodes.

---

## Contrato de variables `ai_*`

Las variables `ai_*` son atributos de `self` (el componente Python). Se leen con `getattr(self, 'ai_*', default)` para evitar errores si el nodo fuente no está en la escena.

| Variable | Dueño (WRITES) | Tipo | Descripción |
|----------|----------------|------|-------------|
| `ai_can_see` | Enemy Perception | bool | Target visible este frame |
| `ai_has_line_of_sight` | Enemy Perception | bool | Raycast sin obstáculos al target |
| `ai_dist` | Enemy Perception | float | Distancia XY al target |
| `ai_target` | Enemy Perception | KX_GameObject\|None | Objeto target |
| `ai_target_pos` | Enemy Perception | Vector\|None | Posición del target si visible |
| `ai_last_seen_pos` | Enemy Perception | Vector\|None | Última posición vista (persiste) |
| `ai_hp` | Damage Receiver | float | HP actual |
| `ai_hp_max` | Damage Receiver | float | HP máximo |
| `ai_is_dead` | Damage Receiver | bool | True cuando HP ≤ 0 |
| `ai_took_damage` | Damage Receiver | bool | True el frame en que llegó daño |
| `ai_recent_damage` | Damage Receiver | bool | Alias de `ai_took_damage` |
| `ai_state` | AI State Machine | str | Estado actual del SM |
| `ai_prev_state` | AI State Machine | str | Estado anterior |
| `ai_state_time` | AI State Machine | float | Segundos en estado actual |
| `ai_alert_level` | AI State Machine | float | 0–1 para HUD/shader |
| `ai_attack_requested` | AI State Machine | bool | True cuando estado == ATTACK |
| `ai_reload_requested` | AI State Machine | bool | True sin ammo y no DEAD |
| `ai_move_target` | AI State Machine / Navigation Follow Path | Vector\|None | Destino de movimiento |
| `ai_in_attack_range` | Enemy Combat | bool | Distancia ≤ Attack Range |
| `ai_is_moving` | Enemy Movement | bool | Velocidad > 0.1 |
| `ai_nav_path` | Navigation Pathfinder | list[Vector] | Camino BFS actual |
| `ai_nav_index` | Navigation Pathfinder / Follow Path | int | Índice del waypoint actual |
| `ai_reached_destination` | Navigation Follow Path | bool | Path completado |

---

## Canales de daño (globalDict)

| Canal | Escribe | Lee |
|-------|---------|-----|
| `_dmg_<nombre>` | EnemyCombat, Player Hitscan | Damage Receiver |
| `_ai_dmg_<nombre>` | EnemyAI (melee/ranged) | EnemyAI (prefab interno) |
| `_ai_dmg_src_<nombre>` | EnemyAI (knockback) | EnemyAI (prefab interno) |

> **Importante:** Los canales `_dmg_*` y `_ai_dmg_*` son distintos. El sistema modular usa `_dmg_*`; el prefab Enemy AI usa `_ai_dmg_*` para su propio sistema interno.

---

## Nodos en esta categoría

### Movimiento primitivo

| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Seek](seek.md) | Exec | Perseguir objetivo con frenado de llegada |
| [Flee](flee.md) | Exec | Huir del objetivo hasta radio seguro |
| [Patrol](patrol.md) | Exec | Patrullar entre 2–8 waypoints |
| [Wander](wander.md) | Exec | Deambular en dirección aleatoria periódica |

### Detección

| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Distance Check](distance-check.md) | Branch (Near/Far) | Bifurcar por distancia al objetivo |
| [Line Of Sight](line-of-sight.md) | Branch (Visible/Hidden) | Raycast de línea de visión |

### Sistema Modular

| Nodo | Exec Order | Descripción |
|------|-----------|-------------|
| [Enemy Perception](enemy-perception.md) | 40 | FOV + raycast + memoria |
| [Damage Receiver](damage-receiver.md) | 42 | HP y canal de daño |
| [AI State Machine](ai-state-machine.md) | 50 | Máquina de estados IDLE→DEAD |
| [Enemy Decision](enemy-decision.md) | 51 | Branch por `ai_can_see` |
| [Navigation Pathfinder](navigation-pathfinder.md) | 52 | BFS sobre waypoints |
| [Navigation Follow Path](navigation-follow-path.md) | 54 | Seguir path, detección de bloqueo |
| [Enemy Movement](enemy-movement.md) | 55 | Movimiento suavizado |
| [Enemy Rotation](enemy-rotation.md) | 56 | Rotación suave hacia target |
| [Enemy Combat](enemy-combat.md) | 58 | Ataque con cooldown |
| [Enemy Animation](enemy-animation.md) | 60 | Animaciones por estado |
| [Enemy Hit Flash](enemy-hit-flash.md) | 62 | Flash de color al recibir daño |

### Prefab

| Nodo | Descripción |
|------|-------------|
| [Enemy AI](enemy-ai.md) | Todo-en-uno: estados, detección, movimiento, combate, animaciones |
