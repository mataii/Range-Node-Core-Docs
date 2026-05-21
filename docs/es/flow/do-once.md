# Do Once

**Tipo:** Branch (True / False)  
**Categoría:** FLOW

Ejecuta el camino `True` solo la primera vez que se dispara. En todas las llamadas posteriores ejecuta el camino `False`. Útil para inicializaciones únicas o efectos de entrada que no deben repetirse.

## Propiedades

Ninguna — el nodo solo mantiene un flag interno.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| True | Salida | Exec (primera llamada) |
| False | Salida | Exec (llamadas posteriores) |

## Comportamiento

```python
if not hasattr(self, '_do1_<nombre>'):
    self._do1_<nombre> = False
if not self._do1_<nombre>:
    self._do1_<nombre> = True
    # → True path
else:
    # → False path
```

- **Primera llamada:** el flag no existe → se crea como False → se pone a True → ejecuta `True`.
- **Todas las siguientes:** el flag ya es True → ejecuta `False`.

El estado persiste mientras el objeto exista en escena.

## Uso típico

### Inicialización de blackboard

```
[OnUpdate] → [Do Once]
                ├── True  ──► [BT Set Blackboard: patrol_idx = 0]
                │                   → [BT Set Blackboard: initialized = True]
                └── False ──► [lógica normal]
```

### Sonido de entrada a zona

```
[OnCollision: zone_trigger] → [Do Once]
                                  ├── True  ──► [BTCustomTask: play_sound('alert')]
                                  └── False ──► (silencio)
```

### Spawn de loot al morir (una sola vez)

```
[BT Condition: ai_is_dead]
    └── True ──► [Do Once]
                    ├── True  ──► [BTCustomTask: self.own.scene.addObject('Coin', self.own, 0)]
                    └── False ──► (ya droppeó)
```

## Resetear el estado

Para reiniciar el Do Once desde código:

```python
# En un BTCustomTask:
var = '_do1_' + 'NombreDelNodo'.replace(' ', '_')
if hasattr(self, var):
    delattr(self, var)
```

O directamente:
```python
self._do1_NombreDelNodo = False
```

## Notas

- El nombre del nodo en el editor determina el nombre de la variable. Renombrar el nodo equivale a resetear su estado.
- Si hay dos nodos Do Once con el mismo nombre en el mismo componente, compartirán la variable — siempre dar nombres únicos.
- El camino `False` se ejecuta desde la segunda llamada en adelante, no solo cuando el nodo ya ha disparado. Conectar a `False` es útil para continuar la ejecución normal sin volver a hacer la inicialización.
