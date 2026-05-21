# Add Object

**Tipo:** Acción  
**Categoría:** OBJECT

Instancia un objeto en la escena usando `scene.addObject()`. El objeto debe existir en una capa inactiva.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Object | String | `"object"` | Nombre del objeto a instanciar |
| Reference | String | `""` | Objeto de referencia para posición/orientación. Vacío = `self.own` |
| Lifetime | Int | `0` | Vida en frames (0 = infinito) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
scene.addObject('enemy_01', self.own, 0)
```

## Uso típico

### Spawn de enemigo al activar trigger

```
[On Collision: Property="spawn_trigger"] → [Add Object: Object="enemy_01", Lifetime=0]
```

### Instanciar proyectil

```
[Weapon Fire Executor] → [Add Object:
                              Object = "bullet_prefab"
                              Reference = "gun_muzzle"
                              Lifetime = 180]
```

## Notas

- El objeto instanciado hereda la posición y orientación del objeto de referencia.
- `scene.addObject()` devuelve el objeto instanciado, pero este nodo no expone ese valor. Usa `BTCustomTask` si necesitas la referencia.
- El objeto fuente debe estar en una capa inactiva de la escena (no en la capa activa).
