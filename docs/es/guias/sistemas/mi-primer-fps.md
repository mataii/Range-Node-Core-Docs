# Mi Primer FPS

**Dificultad:** Principiante  
**Método A (FPS Tools):** ~15 minutos  
**Método B (pipeline modular):** ~45 minutos

Construye un controlador de primera o tercera persona funcional desde cero. Este tutorial cubre dos enfoques — elige el que mejor se adapte a tu proyecto.

---

## Qué vas a construir

Un personaje jugable que:

- Se mueve con WASD y corre con Shift
- Salta con Space
- Mira alrededor con el ratón
- Opcionalmente rastrea salud, munición y stamina como Game Properties

<!-- screenshot: vista FPS funcionando en Range Game Engine -->

---

## Requisitos previos

- Completado [Primeros Pasos](../../primeros-pasos.md)
- Una escena con al menos un objeto suelo (mesh estático con física)

---

## Configuración de la escena

Ambos métodos usan la misma configuración de escena.

### 1. Crear el objeto personaje

Añade una Cápsula (o cualquier mesh) para representar al jugador:

- **Tipo de física:** Character
- **Nombre:** `player` (recomendado para mayor claridad)

En Range: selecciona el mesh → panel de Propiedades → **Physics** → Physics Type → **Character**.

### 2. Añadir una cámara

Añade un objeto Camera (`Shift + A` → Camera):

- Posiciónala dentro de la cápsula aproximadamente a **nivel de los ojos** (Z ≈ 0.7 relativo al centro de la cápsula)
- Haz la cámara hija del jugador: selecciona la cámara, luego `Shift`-selecciona el jugador → `Ctrl + P` → **Object**
- Rota la cámara para que apunte hacia adelante (Rotation X = 90° en el sistema de coordenadas de Range)
- **Nombre:** `fps_camera`

!!! note "Cámara para Tercera Persona"
    Para configuración en tercera persona, la cámara no necesita ser hija del jugador — FPS Tools (Método A) gestiona la posición de la cámara automáticamente cuando **Perspective = Third Person**.

<!-- screenshot: jerarquía de la escena mostrando fps_camera como hija de player -->

---

## Método A — FPS Tools

**Úsalo cuando:** prototipado rápido, game jams, o para aprender qué necesita un controlador FPS completo antes de construirlo tú mismo.

**Limitación:** el controlador es un sistema autocontenido. Personalizar comportamientos individuales — un wall-run, una curva de salto personalizada, mapeos de entrada únicos más allá de las propiedades disponibles — requiere cambiar al Método B.

### Paso 1 — Crear el NodeTree

Selecciona el objeto `player`. Abre el **Node Editor**, haz clic en **New** para crear un NodeTree.

### Paso 2 — Añadir los nodos

- `Shift + A` → **EVENT → On Update**
- `Shift + A` → **FPS → FPS Tools**

### Paso 3 — Configurar FPS Tools

Selecciona el nodo FPS Tools y establece lo siguiente en su panel de propiedades:

| Propiedad | Valor | Notas |
|-----------|-------|-------|
| Camera | `fps_camera` | Tu objeto cámara |
| Perspective | `First Person` | O `Third Person` |
| Distance | `4.0` | Solo se usa en modo Tercera Persona |
| Walk Speed | `5.0` | m/s |
| Sprint Speed | `10.0` | m/s |
| Jump Force | `7.0` | |
| Health | `True`, Max=100 | Crea una Game Property `health` |
| Ammo | `True`, Max=30 | Crea una Game Property `ammo` |

### Paso 4 — Conectar

Arrastra desde la salida **Exec** de **On Update** hasta la entrada **In** de **FPS Tools**:

```
[On Update] ──Exec──► [FPS Tools]
                          Camera=fps_camera
                          Perspective=First Person
                          Walk Speed=5.0
                          Jump Force=7.0
```

### Paso 5 — Registrar el componente

1. En el panel lateral del Node Editor (`N` → pestaña **RNC**), haz clic en **Info Componente**
2. En el Editor de Propiedades → pestaña **Game** → **Game Components**:
   - Elimina el placeholder `module` si existe
   - Haz clic en **Add RNC Component** → pega la cadena copiada → confirma
   - Haz clic en **Assign NodeTree**

Si necesitas repasar este flujo, consulta [Primeros Pasos — Registrar el componente](../../primeros-pasos.md).

### Paso 6 — Ejecutar

Presiona `P` sobre el viewport 3D.

**Resultado esperado:** el personaje se mueve con WASD, corre con Shift, salta con Space y el ratón controla la dirección de la cámara.

<!-- screenshot: vista en primera persona con movimiento funcionando en modo play -->

---

## Método B — Pipeline modular

**Úsalo cuando:** necesitas control preciso sobre comportamientos individuales, estás construyendo para un juego de producción, o necesitas reemplazar o extender partes específicas del controlador.

**Ventaja sobre el Método A:** cada nodo del pipeline puede reemplazarse de forma independiente. Puedes intercambiar Player Movement por un nodo personalizado, añadir un comportamiento de agacharse entre Player Jump y Player Movement, o reemplazar Mouse Look con un sistema de cámara cinemático — sin tocar los demás nodos.

### Entendiendo el pipeline

El Método B divide el controlador FPS en cuatro responsabilidades independientes:

| Nodo | Responsabilidad | Categoría |
|------|----------------|-----------|
| Player Input | Lee teclado y ratón cada frame, publica estado `_pi_*` | PLAYER |
| Player Movement | Aplica movimiento WASD usando el estado `_pi_*` | PLAYER |
| Player Jump | Aplica física de salto usando el estado `_pi_*` | PLAYER |
| Mouse Look | Rota el personaje (guiñada) y la cámara (cabeceo) | FPS |

Player Input se ejecuta primero cada frame y escribe su resultado en variables de instancia `self._pi_*` (por ejemplo, `self._pi_forward`, `self._pi_jump`, `self._pi_is_sprinting`). Los nodos de movimiento y salto leen esas variables. Encadenarlos en secuencia garantiza un orden de ejecución consistente y determinista.

### Paso 1 — Crear el NodeTree

Selecciona el objeto `player`. Abre el Node Editor, haz clic en **New**.

### Paso 2 — Construir la cadena de movimiento

Añade cada nodo con `Shift + A`:

- **EVENT → On Update**
- **PLAYER → Player Input**
- **PLAYER → Player Movement**
- **PLAYER → Player Jump**

Conéctalos de izquierda a derecha:

```
[On Update] → [Player Input] → [Player Movement] → [Player Jump]
```

**Configurar Player Input** (teclas — se muestran los valores por defecto):

| Propiedad | Por defecto |
|-----------|-------------|
| Forward | `W` |
| Backward | `S` |
| Left | `A` |
| Right | `D` |
| Jump | `Space` |
| Sprint | `Left Shift` |

**Configurar Player Movement:**

| Propiedad | Valor |
|-----------|-------|
| Walk Speed | `5.0` |
| Sprint Speed | `10.0` |
| Physics | `Character` |

**Configurar Player Jump:**

| Propiedad | Valor |
|-----------|-------|
| Jump Force | `7.0` |
| Physics | `Character` |

### Paso 3 — Añadir el control de cámara

Añade un segundo `On Update` y un nodo `Mouse Look`:

- `Shift + A` → **EVENT → On Update** (un segundo)
- `Shift + A` → **FPS → Mouse Look**

Conéctalos:

```
[On Update] → [Mouse Look]
                  Camera=fps_camera
                  Sensitivity=2.0
                  Clamp Pitch=1.4
```

**Configurar Mouse Look:**

| Propiedad | Valor |
|-----------|-------|
| Camera | `fps_camera` |
| Sensitivity | `2.0` |
| Clamp Pitch | `1.4` |
| Invert Y | `False` |

!!! note "Dos nodos On Update"
    Un NodeTree puede contener múltiples nodos On Update. Cada uno inicia una cadena exec independiente que se ejecuta cada frame. Usar dos mantiene la lógica de movimiento y cámara visualmente separadas en el editor. No hay penalización de rendimiento.

### Paso 4 — Grafo de nodos completo

```
Cadena 1 — Movimiento
[On Update] → [Player Input] → [Player Movement] → [Player Jump]

Cadena 2 — Cámara
[On Update] → [Mouse Look: Camera=fps_camera]
```

<!-- screenshot: Node Editor mostrando ambas cadenas en paralelo -->

### Paso 5 — Registrar el componente

Igual que el Método A: **Info Componente** → **Add RNC Component** → **Assign NodeTree**.

### Paso 6 — Ejecutar

Presiona `P`. El resultado es idéntico al Método A — mismo movimiento, mismo salto, mismo control de cámara.

<!-- screenshot: vista en primera persona en modo play — resultado idéntico al Método A -->

---

## Método A vs Método B

| Criterio | Método A (FPS Tools) | Método B (Modular) |
|----------|---------------------|---------------------|
| Tiempo de configuración | ~5 min | ~20 min |
| Añadir wall-run | No es posible | Insertar un nodo entre Player Input → Player Movement |
| Curva de salto personalizada | No es posible | Reemplazar Player Jump por un nodo personalizado |
| Añadir una nueva acción de entrada | Limitado a las propiedades disponibles | Ramificar tras Player Input con cualquier nodo |
| Reemplazar el sistema de cámara | No es posible | Reemplazar Mouse Look por cualquier nodo |
| Depurar subsistemas individuales | Sin visibilidad interna | Desconectar un nodo, añadir Print para inspeccionar estado |
| Extender a multijugador local | Muy difícil | Directo — todos los nodos operan sobre `self` |
| Migración prototipo → producción | Requiere reconstruir desde cero | No es necesario reconstruir |

**Regla general:** comienza con el Método A para validar el concepto del juego. Cambia al Método B antes de añadir cualquier mecánica que FPS Tools no exponga como propiedad.

La guía [Monolítico vs Modular](../patrones/monolitico-vs-modular.md) cubre esta decisión en profundidad para todos los sistemas de RNC — no solo el controlador FPS.

---

## Siguientes pasos

- Añadir un enemigo que reaccione al jugador → [IA del enemigo](ia-enemigo.md)
- Añadir salud y daño → *(próximamente)*
- Entender la decisión de arquitectura → [Monolítico vs Modular](../patrones/monolitico-vs-modular.md)
- Referencias de nodos: [FPS Tools](../../fps/fps-tools.md), [Mouse Look](../../fps/mouse-look.md), [Player Input](../../player/player-input.md), [Player Movement](../../player/player-movement.md), [Player Jump](../../player/player-jump.md)
