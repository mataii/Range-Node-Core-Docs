# Set Property

**Tipo:** Acción  
**Categoría:** DATA

Escribe un valor en una propiedad BGE del objeto que ejecuta el componente.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Property | String | `"prop"` | Nombre de la propiedad BGE |
| Value | Any | `0` | Valor a escribir. Acepta socket de datos |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |
| Value | Entrada | Dato (cualquier tipo) |

## Código generado

```python
self.own['score'] = 0
```

## Uso típico

### Resetear puntuación

```
[On Start] → [Set Property: prop="score", Value=0]
```

### Actualizar vida tras recibir daño

```
[On Message: Subject="take_damage"]
    → [BTCustomTask: hp = self.own.get('health',100) - 10]
    → [Set Property: prop="health", Value={hp}]
```

### Marcar objeto como visitado

```
[On Collision: Object="player"] → [Set Property: prop="visited", Value=True]
```

## Notas

- Si la propiedad no existe aún en el objeto, se crea automáticamente con el tipo del valor asignado.
- BGE puede convertir tipos implícitamente — asegúrate de usar el tipo correcto para evitar comportamientos inesperados.
