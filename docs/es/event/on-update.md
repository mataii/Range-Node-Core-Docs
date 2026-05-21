# On Update

**Tipo:** Entry Point  
**Categoría:** EVENT

Punto de entrada que ejecuta cada frame. Es la raíz de la mayoría de los árboles de lógica continua — movimiento, IA, detección de input, etc.

## Propiedades

Ninguna.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Exec | Salida | Exec |

No tiene socket de entrada — es la raíz del árbol.

## Cuándo ejecuta

**Cada frame**, mientras el objeto exista en escena. La frecuencia depende del framerate del juego. Para lógica que no deba ejecutar cada frame, usar [Delay](delay.md), [Gate](gate.md), o los nodos AI con `Perception Interval`.

## Uso típico

### Pipeline de IA completo

```
[On Update] → [Enemy Perception]
                    → [Damage Receiver]
                    → [AI State Machine]
                    → [Enemy Movement]
                    → [Enemy Animation]
```

### Input del jugador

```
[On Update] → [On Key Press: W, Held]
                  ├── True  ──► [Player Movement: forward]
                  └── False ──► (nada)
```

### Timer periódico

```
[On Update] → [Delay: 2.0s, Repeat=True]
                  └── True ──► [BTCustomTask: update_minimap()]
```

### Múltiples On Update en el mismo árbol

Un árbol puede tener varios On Update. Cada uno es una cadena independiente que se ejecuta en el mismo frame:

```
[On Update] → [lógica de movimiento]

[On Update] → [lógica de combate]

[On Update] → [lógica de animación]
```

Esto es equivalente a tener todo en cadena, pero permite organizar el grafo visualmente.

## Nota sobre el botón del editor

El nodo On Update muestra un botón **"Copy component to clipboard"** en el panel de propiedades. Al pulsarlo, copia el código Python generado del componente completo al portapapeles — útil para depurar o inspeccionar el código compilado.

## Notas

- El código generado por On Update se coloca en el método `update(self)` del componente.
- `Range.logic.deltaTime()` está disponible en cualquier nodo conectado a On Update — es el tiempo en segundos desde el frame anterior.
- Si el juego va lento (framerate bajo), `deltaTime` es mayor — los nodos que usan `deltaTime` (movimiento, timers) compensan automáticamente.
- Para tareas que no necesitan ejecutar cada frame, usar el sistema modular con `Perception Interval`, o envolver en un nodo [Gate](../flow/gate.md) o [Delay](../flow/delay.md).
