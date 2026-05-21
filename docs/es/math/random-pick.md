# Random Pick

**Tipo:** Dato  
**Categoría:** MATH

Selecciona y devuelve un elemento aleatorio de una lista de valores usando `random.choice`.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Items | String | `"[]"` | Lista Python como cadena, evaluada con `eval()` |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Value | Salida | Dato (cualquier tipo) |

## Código generado

```python
__import__('random').choice(['idle', 'patrol', 'wander'])
```

## Uso típico

### Seleccionar estado de IA aleatorio

```
[On State Enter: FSM ID="ai", State="choosing"]
    └── On Enter ──► [Set State:
                          State = {Random Pick: Items=['patrol','wander','guard']}]
```

### Seleccionar efecto de sonido aleatorio

```
[On Collision: Property="footstep"]
    → [Play Sound: File={Random Pick: Items=['step1.wav','step2.wav','step3.wav']}]
```

### Asignar color aleatorio

```
[On Start] → [BTCustomTask:
                  colors = [[1,0,0,1],[0,1,0,1],[0,0,1,1]]
                  self.own.color = {Random Pick: Items=colors}]
```

## Notas

- La lista se evalúa con `eval()` en tiempo de compilación. Puede contener cualquier tipo de Python válido: strings, números, listas anidadas.
- Si la lista está vacía, `random.choice` lanza `IndexError`.
