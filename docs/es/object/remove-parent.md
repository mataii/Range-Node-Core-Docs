# Remove Parent

**Tipo:** Acción  
**Categoría:** OBJECT

Elimina el padre del objeto actual, dejándolo como objeto de nivel raíz en la escena.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
self.own.removeParent()
```

## Uso típico

### Soltar objeto recogido

```
[On Key Press: Key=E, Mode=PRESSED] → [Remove Parent]
```

### Desadjuntar al morir

```
[On Message: Subject="player_dead"] → [Remove Parent]
```

## Notas

- Si el objeto no tiene padre, `removeParent()` no tiene efecto.
- Al eliminar el padre, el objeto mantiene su posición de mundo actual y queda fijo en la escena con physics independiente.
