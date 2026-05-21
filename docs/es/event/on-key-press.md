# On Key Press

**Tipo:** Branch (True / False)  
**Categoría:** EVENT

Detecta el estado de una tecla del teclado y bifurca la ejecución. Soporta tres modos: primera pulsación, tecla sostenida y primera liberación.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Key | Enum | `Space` | Tecla a detectar (selección con captura interactiva) |
| Mode | Enum | `Just Pressed` | Momento del evento |

### Modos disponibles

| Modo | Constante | Descripción |
|------|-----------|-------------|
| Just Pressed | `activated` | True solo el **primer frame** en que la tecla se presiona |
| Held | `active` | True **cada frame** mientras la tecla está sostenida |
| Just Released | `released` | True solo el **primer frame** después de soltar la tecla |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| True | Salida | Exec (tecla en el modo configurado) |
| False | Salida | Exec (tecla no en ese modo) |

## Teclas disponibles

Letras A–Z, Space, Left/Right Shift, Left/Right Ctrl, Tab, Escape, Enter, números 1–5, F1–F5, flechas de cursor (Up, Down, Left, Right).

## Comportamiento

El código generado:
```python
if Range.logic.keyboard.inputs[Range.events.SPACEKEY].activated:
    # → True path
else:
    # → False path
```

El nodo usa las constantes de `Range.events` (ej. `SPACEKEY`, `WKEY`, `LEFTSHIFTKEY`) y los atributos de estado del input (`activated`, `active`, `released`).

## Cómo asignar la tecla

En el editor de nodos, el botón de la tecla abre un capturador interactivo: al pulsarlo el editor espera una pulsación y asigna automáticamente la tecla correspondiente. No es necesario escribir el nombre de la tecla manualmente.

## Uso típico

### Movimiento WASD (Held)

```
[On Update] → [On Key Press: W, Held]
                  ├── True  ──► [Player Movement: forward]
                  └── False

[On Update] → [On Key Press: S, Held]
                  ├── True  ──► [Player Movement: backward]
                  └── False
```

### Interacción (Just Pressed)

```
[On Update] → [On Key Press: E, Just Pressed]
                  ├── True  ──► [Interaction System]
                  └── False
```

### Toggle con Flip Flop (Just Pressed)

```
[On Update] → [On Key Press: Tab, Just Pressed]
                  └── True ──► [Flip Flop]
                                  ├── True  ──► [abrir mapa]
                                  └── False ──► [cerrar mapa]
```

### Disparo continuo (Held)

```
[On Update] → [On Key Press: Space, Held]
                  └── True ──► [Weapon Fire Executor]
```

### Acción al soltar (Just Released)

```
[On Update] → [On Key Press: Space, Just Released]
                  └── True ──► [BTCustomTask: charge_attack_execute()]
```

## Diferencia de modos

| Situación | Just Pressed | Held | Just Released |
|-----------|-------------|------|---------------|
| Primer frame de pulsación | **True** | True | False |
| Frames siguientes sostenida | False | **True** | False |
| Frame de liberación | False | False | **True** |
| Frames sin pulsar | False | False | False |

## Notas

- `Just Pressed` es el modo más común para acciones que deben ocurrir una sola vez por pulsación (saltar, interactuar, disparar single).
- `Held` es para acciones continuas (moverse, disparar auto, cargar).
- `Just Released` es para acciones que se ejecutan al soltar (ej. lanzar tras cargar, soltar un objeto).
- El nodo debe estar conectado a **On Update** para comprobar el estado cada frame. Si se conecta a On Start, solo comprobará la tecla en el primer frame.
- Para teclado del gamepad, usar los nodos de la categoría **GAMEPAD** en lugar de este.
