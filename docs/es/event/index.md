# Categoría EVENT

La categoría **EVENT** contiene los puntos de entrada del árbol de nodos y los detectores de eventos del motor. Son los nodos que **inician** la ejecución o la desencadenan en respuesta a algo que ocurre en la escena.

## Tres grupos

### Puntos de entrada (Entry Points)

Nodos sin socket In. Son la raíz del árbol — el compilador genera un método Python separado por cada uno.

| Nodo | Cuándo ejecuta |
|------|----------------|
| [On Start](on-start.md) | Una sola vez al crear el componente |
| [On Update](on-update.md) | Cada frame |

### Detectores de evento

Nodos con socket In que bifurcan la ejecución según si ocurrió un evento:

| Nodo | Tipo | Evento detectado |
|------|------|-----------------|
| [On Key Press](on-key-press.md) | Branch (True/False) | Tecla presionada / sostenida / liberada |
| [On Collision](on-collision.md) | Branch (Hit/None) | Colisión con otro objeto |
| [On Mouse Move](on-mouse-move.md) | Branch (Moving/Still) | Movimiento del cursor |
| [On Timer](on-timer.md) | Branch (Elapsed/Waiting) | Cuenta atrás propia |
| [On Property Change](on-property-change.md) | Branch (Changed/Same) | Cambio de propiedad BGE |

### Bus de mensajes

Par de nodos para comunicación entre objetos sin referencias directas:

| Nodo | Rol |
|------|-----|
| [On Message](on-message.md) | Receptor — espera un mensaje con subject |
| [Node Broadcast](node-broadcast.md) | Emisor — envía el mensaje a todos los receptores |

---

## Cuándo usar cada uno

```
Inicio único              → On Start
Cada frame                → On Update
Input de teclado          → On Key Press
Colisión física           → On Collision
Movimiento del ratón      → On Mouse Move
Evento periódico interno  → On Timer
Reacción a propiedad BGE  → On Property Change
Comunicación entre objetos → On Message + Node Broadcast
```

## Múltiples entry points en el mismo árbol

Un árbol puede tener tanto On Start como On Update (y varios de cada tipo). El compilador genera un método separado por cada uno:

```python
def start(self):
    # → nodos conectados a On Start

def update(self):
    # → nodos conectados a On Update
```

Todos los demás nodos (detectores, FLOW, AI…) deben estar conectados a uno de estos entry points para ejecutarse.

## On Timer vs Delay (FLOW)

| On Timer | Delay (FLOW) |
|----------|-------------|
| Punto de entrada propio | Requiere OnUpdate como raíz |
| Countdown con `deltaTime()` | Reloj absoluto `getClockTime()` |
| Expone socket Progress (0→1) | Sin progreso |
| Repeat por defecto = True | Repeat por defecto = False |

## On Message vs sendMessage() nativo

`Node Broadcast` + `On Message` usan `globalDict` — funcionan entre **componentes RNC**. El `sendMessage()` nativo de Range usa el sistema de Logic Bricks y no es compatible con este bus.
