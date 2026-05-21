# Set Mass

**Tipo:** Acción  
**Categoría:** PHYSICS

Cambia la masa del objeto en tiempo de ejecución, afectando cómo responde a fuerzas e impulsos.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Mass | Float | `1.0` | Nueva masa del objeto en kg |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
self.own.mass = 1.0
```

## Uso típico

### Objeto que absorbe agua y se vuelve más pesado

```
[On Collision: Object="water"]
    → [Set Mass: Mass=10.0]
```

### Resetear masa al salir del agua

```
[On End Collision: Object="water"]
    → [Set Mass: Mass=1.0]
```

### Objeto invencible (masa enorme para ignorar colisiones)

```
[On Start] → [Set Mass: Mass=999.0]
```

## Notas

- La masa `0.0` convierte el objeto en estático (no responde a fuerzas).
- Una masa mayor requiere más fuerza para mover el objeto a la misma aceleración (F = ma).
- No confundir con el escalado — cambiar la masa no afecta visualmente al objeto.
