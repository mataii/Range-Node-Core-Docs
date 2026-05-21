# Replace Mesh

**Tipo:** Acción  
**Categoría:** OBJECT

Reemplaza la malla del objeto con la malla de otro objeto de la escena usando `replaceMesh()`.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Mesh Object | String | `"mesh_source"` | Nombre del objeto cuya malla se usará como fuente |
| Use Display | Bool | `True` | Reemplaza la malla de visualización |
| Use Physics | Bool | `True` | Reemplaza también la malla de física |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
self.own.replaceMesh('mesh_source', True, True)
```

## Uso típico

### Cambiar modelo de arma al equipar

```
[On Message: Subject="equip_rifle"]
    → [Replace Mesh: Mesh Object="mesh_rifle"]
```

### Daño visual progresivo

```
[On Update] → [If Property: prop="health", Op=<, Value=50]
                  └── True ──► [Replace Mesh: Mesh Object="damaged_hull"]
```

### LOD manual

```
[On Update] → [If Property: prop="dist_to_cam", Op=>, Value=20]
                  ├── True ──► [Replace Mesh: Mesh Object="low_poly_version"]
                  └── False ──► [Replace Mesh: Mesh Object="high_poly_version"]
```

## Notas

- El objeto fuente (`Mesh Object`) debe existir en la escena — puede estar en capa inactiva.
- Si `Use Physics=True`, la física se recalcula para la nueva malla — puede ser costoso en mallas complejas.
- El material no cambia — solo la geometría.
