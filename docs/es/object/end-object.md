# End Object

**Tipo:** Acción  
**Categoría:** OBJECT

Destruye el objeto que ejecuta el componente al final del frame actual usando `self.own.endObject()`.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |

## Código generado

```python
self.own.endObject()
```

## Uso típico

### Destruir proyectil al impactar

```
[On Collision: Property="solid"] → [End Object]
```

### Destruir enemigo al morir

```
[On Message: Subject="enemy_dead"] → [End Object]
```

### Destruir pickup al recogerlo

```
[On Collision: Object="Player"] → [End Object]
```

## Notas

- `endObject()` programa la destrucción al final del frame — el objeto sigue existiendo durante el frame actual. No continúes ejecutando lógica dependiente del objeto después de llamar a `endObject()`.
- No tiene socket `Out` porque cualquier código posterior es potencialmente peligroso (el objeto está marcado para destrucción).
- Para destruir un objeto diferente al que ejecuta el componente, usa `BTCustomTask: scene.objects['name'].endObject()`.
