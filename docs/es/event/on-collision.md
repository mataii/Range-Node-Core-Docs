# On Collision

**Tipo:** Branch (Hit / None)  
**Categoría:** EVENT

Detecta colisiones físicas con otros objetos usando el sistema de callbacks de colisión de Range. Bifurca a `Hit` cuando hay colisión, con opción de filtrar por propiedad BGE.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Property Filter | String | `""` | Solo detecta objetos que tengan esta propiedad BGE. Vacío = cualquier objeto |
| Consume Event | Bool | `True` | Limpia el flag tras leerlo para evitar re-disparar el mismo frame siguiente |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Hit | Salida | Exec (hay colisión) |
| None | Salida | Exec (sin colisión) |
| Object | Salida | Data (KX_GameObject) |

## Comportamiento

### Mecanismo de callback

El nodo usa `collisionCallbacks` de Range — registra una función de callback en `self.own.collisionCallbacks` la primera vez que se ejecuta:

```python
def _col_cb(obj):
    if obj has property_filter:
        self.own['_rnc_col_<nombre>'] = True
        self.own['_rnc_col_<nombre>_obj'] = obj.name
```

El callback se registra **solo una vez** (guardado por `hasattr`). Range lo llama automáticamente en cada colisión, aunque el nodo no esté en el frame actual.

### Lectura del flag

Cada frame en que el nodo ejecuta, lee la propiedad BGE `_rnc_col_<nombre>`:
- Si es `True` → camino `Hit`.
- Si es `False` → camino `None`.

### Consume Event = True (por defecto)

Después de detectar `Hit`, limpia el flag a `False`. Así el camino `Hit` solo se activa **una vez por colisión**, no en todos los frames siguientes.

### Consume Event = False

El flag permanece `True` hasta que algo externo lo borre. Útil si múltiples nodos necesitan leer la misma colisión.

### Socket Object

Contiene el objeto que colisionó (`_col_incoming`). Puede ser `None` si el objeto ya fue eliminado de escena antes del siguiente frame.

## Uso típico

### Recoger un pickup

```
[On Update] → [On Collision: Property Filter="pickup"]
                  ├── Hit  ──► [BTCustomTask: collect_item(_col_incoming)]
                  └── None ──► (nada)
```

### Zona de daño

```
[On Update] → [On Collision: Property Filter="player"]
                  ├── Hit  ──► [BTCustomTask:
                  │               Range.logic.globalDict['_dmg_' + _col_incoming.name] = 10.0]
                  └── None ──► (nada)
```

### Activar trampa al pisar

```
[On Update] → [On Collision]
                  └── Hit ──► [Do Once]
                                 └── True ──► [BTCustomTask: activate_trap()]
```

### Leer el objeto impactado

El socket `Object` expone `_col_incoming`:

```python
# En un BTCustomTask conectado a Hit:
if _col_incoming:
    print("Colisionó con:", _col_incoming.name)
```

## Filtro de propiedad

Con `Property Filter = "enemy"`: solo dispara `Hit` cuando el objeto que colisiona tiene una propiedad BGE llamada `enemy` (cualquier tipo — Bool, Int, String).

Sin filtro (vacío): cualquier objeto físico que colisione dispara `Hit`, incluyendo el suelo, paredes, etc.

## Notas

- El nodo debe conectarse a **On Update** para leer el flag cada frame.
- El callback de colisión es independiente del árbol — un objeto puede colisionar aunque el árbol no se haya ejecutado ese frame (ej. lag spike). El flag persiste hasta el siguiente frame de On Update.
- Si el objeto colisionado se destruye antes de que se lea el socket `Object`, `_col_incoming` será `None`.
- Para colisiones entre sensores/overlapping (triggers de zona), asegurarse de que el objeto tiene tipo de física correcto (Dynamic, Character o Static con collision enabled).
- Con `Consume = False` y múltiples colisiones en el mismo frame: solo se registra el **último** objeto en el callback (sobrescribe `_obj`).
