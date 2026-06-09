# IA del enemigo

**Dificultad:** Intermedio  
**Método A (prefab Enemy AI):** ~10 minutos  
**Método B (sistema modular):** ~30 minutos

Construye un enemigo que detecta al jugador, lo persigue y ataca cuando se acerca lo suficiente. Este tutorial cubre dos enfoques: un nodo prefab único y un pipeline modular construido con nodos de IA individuales.

---

## Qué vas a construir

Un objeto enemigo que:

- Permanece inactivo hasta que el jugador entra en un radio de detección configurable y un campo de visión
- Transiciona automáticamente entre los estados IDLE, CHASE y ATTACK
- Se mueve hacia el jugador cuando lo detecta
- Ataca cuando está dentro del rango de ataque

<!-- screenshot: enemigo persiguiendo al jugador en Range Game Engine -->

---

## Requisitos previos

- Completado [Primeros Pasos](../../primeros-pasos.md)
- Una escena con un objeto jugador (se recomienda completar [Mi Primer FPS](mi-primer-fps.md) antes)
- Una escena con un objeto enemigo — cualquier mesh sirve

---

## Configuración de la escena

Ambos métodos usan la misma configuración base.

### Objeto jugador
Debe existir en la escena con un nombre reconocible. Los ejemplos a continuación usan `player`.

### Objeto enemigo
- Cualquier mesh (Cápsula, Cubo, o un mesh de personaje)
- **Tipo de física:** Dynamic (o Character para colisiones más precisas)
- Este objeto recibirá el componente de RNC

---

## Método A — Enemy AI (prefab)

**Úsalo cuando:** necesitas un enemigo funcional rápidamente, los comportamientos por defecto se ajustan a tu diseño, o estás prototipando combate antes de comprometerte con una arquitectura de IA definitiva.

**Limitación:** los comportamientos son fijos. Añadir estados personalizados — patrulla, cobertura, huida, o un Behavior Tree — requiere cambiar al Método B.

### Paso 1 — Crear el NodeTree

Selecciona el objeto enemigo. Abre el **Node Editor**, haz clic en **New**.

### Paso 2 — Añadir los nodos

- `Shift + A` → **EVENT → On Update**
- `Shift + A` → **AI → Enemy AI**

### Paso 3 — Configurar Enemy AI

| Propiedad | Valor | Notas |
|-----------|-------|-------|
| Target | `player` | Nombre del objeto jugador en la escena |
| Detection Radius | `15.0` | Distancia en unidades para detectar al jugador |
| Field of View | `120.0` | Grados de visión periférica |
| Chase Speed | `4.0` | Velocidad de movimiento al perseguir |
| Attack Range | `2.0` | Distancia a la que el enemigo ataca |
| HP | `100` | Puntos de vida del enemigo |

### Paso 4 — Conectar

```
[On Update] ──Exec──► [Enemy AI]
                          Target=player
                          Detection Radius=15.0
                          Field of View=120.0
                          Chase Speed=4.0
                          Attack Range=2.0
                          HP=100
```

### Paso 5 — Registrar el componente

Info Componente → Add RNC Component → Assign NodeTree (mismo flujo que en [Primeros Pasos](../../primeros-pasos.md)).

### Paso 6 — Ejecutar

Presiona `P`. El enemigo permanecerá inactivo hasta que el jugador entre en su radio de detección, luego cambiará al estado CHASE y finalmente al estado ATTACK cuando esté suficientemente cerca.

<!-- screenshot: nodo Enemy AI activo, enemigo persiguiendo al jugador -->

---

## Método B — Sistema modular

**Úsalo cuando:** necesitas estados personalizados, múltiples variantes de comportamiento, integración con Behavior Tree, o ajuste individual de subsistemas por enemigo.

El Método B expone la misma lógica subyacente que el Método A, pero como nodos individuales. Cada nodo lee y escribe atributos nombrados en `self` usando un prefijo compartido `ai_*` (por ejemplo, `self.ai_state`, `self.ai_dist`, `self.ai_can_see`). Los nodos deben ejecutarse en un orden fijo — define ese orden a través de tu cadena exec.

!!! note "Enemigo modular simplificado"
    Este tutorial usa **Seek** para el movimiento (movimiento directo en línea hacia el objetivo). Añade [Navigation Pathfinder](../../ai/navigation-pathfinder.md) + [Navigation Follow Path](../../ai/navigation-follow-path.md) cuando necesites pathfinding con consciencia de obstáculos y puntos de paso.

### El pipeline

```
[On Update]
    │
    ├─► [Enemy Perception]   → escribe ai_can_see, ai_dist, ai_target_pos
    │
    ├─► [Damage Receiver]    → escribe ai_hp, ai_is_dead
    │
    ├─► [AI State Machine]   → lee ai_can_see, ai_dist; escribe ai_state
    │
    └─► [Enemy Decision]     → ramifica según ai_can_see
            │
            ├─ Visible ──► [Seek] → [Enemy Rotation] → [Enemy Combat] → [Enemy Animation]
            │
            └─ Hidden  ──►         [Enemy Rotation]                   → [Enemy Animation]
```

### Paso 1 — Crear el NodeTree

Selecciona el objeto enemigo, haz clic en **New** en el Node Editor.

### Paso 2 — Añadir todos los nodos

Añade cada nodo con `Shift + A`:

| Nodo | Categoría |
|------|-----------|
| On Update | EVENT |
| Enemy Perception | AI |
| Damage Receiver | AI |
| AI State Machine | AI |
| Enemy Decision | AI |
| Seek | AI |
| Enemy Rotation | AI |
| Enemy Combat | AI |
| Enemy Animation | AI |

### Paso 3 — Configurar cada nodo

**Enemy Perception:**

| Propiedad | Valor |
|-----------|-------|
| Target | `player` |
| Detection Radius | `15.0` |
| Field of View | `120.0` |

**Damage Receiver:**

| Propiedad | Valor |
|-----------|-------|
| HP | `100` |

**AI State Machine** — usa los estados IDLE / CHASE / ATTACK / DEAD por defecto. No se requiere configuración adicional para esta configuración básica.

**Enemy Decision** — ramifica la ejecución basándose en `ai_can_see`. No se requiere configuración.

**Seek:**

| Propiedad | Valor |
|-----------|-------|
| Target | `player` |
| Speed | `4.0` |
| Arrival Radius | `2.0` |

**Enemy Rotation:**

| Propiedad | Valor |
|-----------|-------|
| Speed | `5.0` |

**Enemy Combat:**

| Propiedad | Valor |
|-----------|-------|
| Attack Range | `2.0` |
| Attack Cooldown | `1.5` |
| Damage | `10` |

**Enemy Animation** — configura con los nombres de action de tu proyecto.

### Paso 4 — Conectar el pipeline

Conecta **On Update → Enemy Perception → Damage Receiver → AI State Machine → Enemy Decision**.

Desde **Enemy Decision**:

- Salida **Visible** → **Seek → Enemy Rotation → Enemy Combat → Enemy Animation**
- Salida **Hidden** → **Enemy Rotation → Enemy Animation** (el enemigo continúa rotando y animándose incluso cuando el jugador no es detectado)

```
[On Update] → [Enemy Perception]
                  → [Damage Receiver]
                  → [AI State Machine]
                  → [Enemy Decision]
                        │
                        ├─ Visible ──► [Seek] → [Enemy Rotation] → [Enemy Combat] → [Enemy Animation]
                        │
                        └─ Hidden  ──►          [Enemy Rotation]                  → [Enemy Animation]
```

<!-- screenshot: Node Editor mostrando el pipeline modular completo conectado -->

### Paso 5 — Registrar el componente

Info Componente → Add RNC Component → Assign NodeTree.

### Paso 6 — Ejecutar

Presiona `P`. El comportamiento es idéntico al Método A para el caso estándar.

---

## Método A vs Método B

| Criterio | Método A (Enemy AI) | Método B (Modular) |
|----------|--------------------|--------------------|
| Tiempo de configuración | ~5 min | ~25 min |
| Añadir patrulla cuando está inactivo | No es posible | Reemplazar la rama `Hidden` con un nodo Patrol |
| Añadir estados de IA personalizados | No es posible | Configurar estados adicionales en AI State Machine |
| Múltiples variantes de enemigo | Solo diferencias en propiedades | Cada variante puede usar un subconjunto diferente de nodos |
| Integración con Behavior Tree | No es posible | Reemplazar Enemy Decision con nodos BT |
| Ajuste de rendimiento por frame | Sin control | Establecer Perception Interval en Enemy Perception |
| Depurar comportamientos individuales | Sin visibilidad interna | Desconectar un nodo, insertar Print para inspeccionar estado `ai_*` |
| Navegación con consciencia de obstáculos | Simplificada e integrada | Añadir Navigation Pathfinder + Navigation Follow Path |

---

## Siguientes pasos

- Añadir pathfinding con consciencia de obstáculos → [Navigation Pathfinder](../../ai/navigation-pathfinder.md)
- Reemplazar Enemy Decision con un Behavior Tree → [categoría AI_BT](../../ai-bt/index.md)
- Referencia completa del pipeline de IA → [categoría AI](../../ai/index.md)
- Referencias de nodos: [Enemy AI](../../ai/enemy-ai.md), [Enemy Perception](../../ai/enemy-perception.md), [AI State Machine](../../ai/ai-state-machine.md), [Seek](../../ai/seek.md)
