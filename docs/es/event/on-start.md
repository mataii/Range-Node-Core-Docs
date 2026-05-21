# On Start

**Tipo:** Entry Point  
**Categoría:** EVENT

Punto de entrada que ejecuta una sola vez cuando el componente Python se crea. Equivalente al método `start()` de un `KX_PythonComponent`.

## Propiedades

Ninguna.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Exec | Salida | Exec |

No tiene socket de entrada — es la raíz del árbol.

## Cuándo ejecuta

**Una sola vez**, en el primer frame en que el componente existe. Si el objeto se destruye y se vuelve a crear (ej. con `addObject`), el On Start del nuevo objeto ejecutará de nuevo.

## Uso típico

### Inicialización de variables

```
[On Start] → [BT Set Blackboard: patrol_idx = 0]
                   → [BT Set Blackboard: hp = 100]
                   → [BT Set Blackboard: mode = 'idle']
```

### Registrar estado inicial en BGE properties

```
[On Start] → [BTCustomTask]
```
```python
self.own['ai_state'] = 'idle'
self.own['ai_hp']    = 100.0
```

### Sonido de aparición

```
[On Start] → [BTCustomTask: play_sound('spawn')]
```

### Inicialización que no debe repetirse (vs On Update + Do Once)

On Start es preferible a `[On Update] → [Do Once]` cuando la inicialización no tiene dependencias de otros nodos que también usen On Update, porque:
- Es semánticamente más claro.
- No consume un tick de `Do Once` cada frame.

## Diferencia con On Update

| On Start | On Update |
|----------|-----------|
| Se ejecuta una vez | Se ejecuta cada frame |
| No recibe `deltaTime` útil | Puede acumular `deltaTime` |
| Para inicialización | Para lógica continua |

## Notas

- El código generado por On Start se coloca en el método `start(self)` del componente.
- Un árbol puede tener **múltiples nodos On Start** — cada uno genera una cadena de código independiente dentro del mismo método `start()`.
- Si el árbol no tiene ningún On Start, el método `start()` del componente queda vacío.
