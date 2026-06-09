# Range Node Core — Manual

**Range Node Core (RNC)** es un addon que agrega un sistema de nodos blueprint al **Range Game Engine** (fork de BGE). Permite programar la lógica de tus objetos de juego visualmente conectando nodos, sin escribir código Python directamente.

## Cómo funciona

El addon compila el grafo de nodos a código Python que se ejecuta como un `KX_PythonComponent` vía `exec()`. Cada nodo genera un fragmento de código; el compilador los encadena en orden de ejecución.

## Categorías disponibles

| Categoría | Descripción |
|-----------|-------------|
| **PLAYER** | Movimiento, salto, inventario, armas, salud, sistemas de juego FPS |
| **AI** | Percepción, máquinas de estado, movimiento, combate enemigo |
| **AI_BT** | Behavior Tree — nodos Selector, Sequence, Task, Decorator |
| **FPS** | Controller FPS clásico (all-in-one legacy) |
| **PHYSICS** | Fuerzas, impulsos, raycasts de física |
| **RAYCAST** | Raycast general entre puntos arbitrarios |
| **EVENT** | OnStart, OnUpdate, OnCollision, OnPropertyChange |
| **FLOW** | Branch, Gate, Delay, Timer |
| **LOGIC** | AND, OR, NOT, Compare |
| **MATH** | Operaciones aritméticas y trigonométricas |
| **OBJECT** | Transformaciones, propiedades BGE |
| **ANIMATION** | Play Action, Stop Action |
| **AUDIO** | Play Sound, Stop Sound |
| **CAMERA** | Seguimiento, FOV |
| **FSM** | Finite State Machine |
| **SAVE** | Sistema de guardado de datos |
| **VEHICLE** | Control de vehículos BGE |
| **DIALOGUE** | Sistema de diálogos |
| **UI** | Textos en pantalla |
| **DEBUG** | Print, Log |

## Convenciones del compilador

- Los nodos **exec** tienen sockets `In` y `Out` y se encadenan secuencialmente.
- Los nodos **branch** tienen dos salidas exec (`True/False` o nombres personalizados) y generan un bloque `if/else`.
- Los nodos **data** son expresiones Python puras; no tienen sockets exec.
- Los datos entre nodos en el mismo objeto se comparten vía variables de instancia (`self._prefijo_*`).
