# String Length

**Tipo:** Dato  
**Categoría:** DATA

Devuelve la longitud de una cadena de texto como entero.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Text | String | `""` | Cadena a medir |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| Text | Entrada | Dato (String) |
| Length | Salida | Dato (Int) |

## Código generado

```python
len(str("hello"))
# resultado: 5
```

## Uso típico

### Validar que un campo no está vacío

```
[On Update] → [BT Condition: {String Length: Text={Get Property: prop="username"}} > 0]
                  └── True ──► [...]  # campo válido
```

### Limitar longitud de entrada

```
[On Update] → [BT Condition: {String Length: Text={Get Property: prop="input"}} < 20]
                  └── True ──► [...]  # puede seguir escribiendo
```

## Notas

- Aplica `str()` al texto de entrada, por lo que acepta números y otros tipos.
- Devuelve `0` para cadenas vacías.
