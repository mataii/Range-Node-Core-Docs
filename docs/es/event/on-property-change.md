# On Property Change

**Tipo:** Branch (Changed / Same)  
**Categoría:** EVENT

Detecta cuando una propiedad BGE del objeto propietario cambia de valor entre frames. Expone el valor nuevo y el anterior como sockets de datos.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Property | Enum | *(propiedades BGE del objeto)* | Propiedad a monitorear |

El desplegable muestra las propiedades BGE definidas en el objeto activo.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Changed | Salida | Exec (el valor cambió) |
| Same | Salida | Exec (sin cambio) |
| New Value | Salida | Data (cualquier tipo) |
| Old Value | Salida | Data (cualquier tipo) |

## Comportamiento

```python
if not hasattr(self, '_prev_<propiedad>'):
    self._prev_<propiedad> = self.own.get('<propiedad>', None)

_pc_new = self.own.get('<propiedad>', None)
_pc_old = self._prev_<propiedad>

if _pc_new != _pc_old:
    self._prev_<propiedad> = _pc_new
    # → Changed
else:
    # → Same
```

El primer frame siempre va a `Same` (se inicializa el valor previo igual al actual).

### Tipos de propiedad compatibles

Todas las propiedades BGE de Range son compatibles: `Bool`, `Int`, `Float`, `String`. La comparación usa `!=` de Python, que funciona para todos los tipos.

## Uso típico

### Reaccionar al cambio de estado de IA

```
[On Update] → [On Property Change: ai_state]
                  ├── Changed ──► [BTCustomTask:
                  │                   print("Estado cambió a:", _pc_new)]
                  └── Same    ──► (nada)
```

### Detectar muerte por HP

```
[On Update] → [On Property Change: ai_hp]
                  └── Changed ──► [BTCustomTask:
                                       if _pc_new <= 0 and _pc_old > 0:
                                           trigger_death_sequence()]
```

### Cambio de nivel/zona

```
# El objeto tiene propiedad BGE "current_zone" (String)
[On Update] → [On Property Change: current_zone]
                  └── Changed ──► [Node Broadcast: subject="zone_changed"]
```

### Trigger de HUD al cambiar HP

```
[On Update] → [On Property Change: player_hp]
                  └── Changed ──► [HUD State Publisher]
```

## Cómo crear propiedades BGE

En Range, las propiedades BGE se crean en el panel de propiedades del objeto (pestaña de lógica de juego):

1. Seleccionar el objeto.
2. Panel de propiedades → Game Properties.
3. Añadir propiedad con nombre y tipo.

El desplegable de On Property Change muestra automáticamente las propiedades del objeto activo en el momento de edición.

## Variables disponibles en nodos siguientes

Cuando el camino `Changed` ejecuta, las variables locales `_pc_new` y `_pc_old` están disponibles:

```python
# En un BTCustomTask conectado a Changed:
print(f"Cambió de {_pc_old} a {_pc_new}")
```

## Notas

- Si la propiedad no existe en el objeto en runtime (`self.own.get(prop, None)` devuelve `None`), el nodo considerará `None != None` como False — no disparará `Changed`. Asegurarse de que la propiedad existe en el objeto.
- Si el valor cambia y vuelve al valor original en el mismo frame, el nodo **sí** detecta el cambio (porque se compara con el valor del frame anterior, no con el original).
- On Property Change es más ligero que un BTCustomTask con comparación manual — solo hace un `get` y una comparación por frame.
- Para monitorear propiedades de **otro objeto** (no del propietario), usar un BTCustomTask con `other_obj.get('prop')` y comparación manual.
