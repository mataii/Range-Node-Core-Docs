# Flip Flop

**Tipo:** Branch (True / False)  
**Categoría:** FLOW

Alterna entre los caminos `True` y `False` en cada llamada. La primera llamada ejecuta `True`, la segunda `False`, la tercera `True`, y así sucesivamente. Útil para mecánicas de toggle.

## Propiedades

Ninguna — el nodo solo mantiene un flag de estado.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| True | Salida | Exec (llamadas impares: 1ª, 3ª, 5ª…) |
| False | Salida | Exec (llamadas pares: 2ª, 4ª, 6ª…) |

## Comportamiento

```python
if not hasattr(self, '_ff_<nombre>'):
    self._ff_<nombre> = False
self._ff_<nombre> = not self._ff_<nombre>
if self._ff_<nombre>:
    # → True path
else:
    # → False path
```

El flag empieza en `False` y se **niega antes del check**, por lo que:

| Llamada | Estado antes | Negado | Camino |
|---------|-------------|--------|--------|
| 1ª | False | **True** | True |
| 2ª | True | **False** | False |
| 3ª | False | **True** | True |
| … | … | … | … |

## Uso típico

### Puerta abre/cierra

```
[OnCollision: button] → [Flip Flop]
                            ├── True  ──► [BTCustomTask: open_door()]
                            └── False ──► [BTCustomTask: close_door()]
```

### Alternar animación de ataque (izquierda/derecha)

```
[BT Condition: attack_requested]
    └── True ──► [Flip Flop]
                    ├── True  ──► [BT Play Animation: "AttackLeft"]
                    └── False ──► [BT Play Animation: "AttackRight"]
```

### Luz parpadeante (con Delay)

```
[OnUpdate] → [Delay: 0.5s, Repeat=True]
                 └── True ──► [Flip Flop]
                                 ├── True  ──► [BTCustomTask: light.energy = 10]
                                 └── False ──► [BTCustomTask: light.energy = 0]
```

### Toggle de modo sigilo

```
[OnKeyboard: Tab] → [Flip Flop]
                        ├── True  ──► [BT Set Blackboard: stealth = True]
                        └── False ──► [BT Set Blackboard: stealth = False]
```

## Notas

- El estado persiste mientras el objeto exista. Si el objeto se destruye y se vuelve a crear, el Flip Flop empieza desde `True` otra vez.
- El nombre del nodo determina la variable interna — dar nombres únicos si hay varios Flip Flop en el mismo componente.
- A diferencia de Do Once, Flip Flop **nunca termina** — siempre alterna indefinidamente.
- Para resetear a estado inicial: `self._ff_NombreDelNodo = False`.
