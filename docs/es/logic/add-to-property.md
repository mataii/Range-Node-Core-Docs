# Add To Property

**Tipo:** Acción  
**Categoría:** LOGIC

Suma un valor a una propiedad BGE del objeto (`self.own[prop] += amount`).

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Property | String | `"score"` | Nombre de la propiedad BGE |
| Amount | Float | `1.0` | Valor a sumar. Puede ser negativo para restar |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |
| Amount | Entrada | Dato (Float) |

## Código generado

```python
self.own['score'] += 1.0
```

## Uso típico

### Acumular puntos

```
[On Collision: Property="pickup"] → [Add To Property: prop="score", Amount=10]
```

### Contar frames en un estado

```
[On Update] → [On State: FSM ID="ai", State="patrol"]
                  └── True ──► [Add To Property: prop="patrol_frames", Amount=1]
```

### Reducir vida (restar)

```
[On Message: Subject="take_damage"] → [Add To Property: prop="health", Amount=-10]
```

## Notas

- La propiedad debe existir en el objeto con un tipo numérico. Si no existe o es string, Python lanzará `TypeError`.
- Para escribir un valor absoluto, usa [Set Property](../data/set-property.md).
