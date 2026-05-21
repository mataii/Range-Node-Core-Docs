# Print

**Tipo:** Acción  
**Categoría:** DEBUG

Imprime un mensaje en la consola de Range Game Engine. Equivalente a `print()` de Python.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Message | String | `"debug"` | Texto a imprimir. Acepta sockets de datos conectados |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
print("debug")
```

Si se conecta un socket de datos al mensaje:

```python
print(self.own.get('score', None))
```

## Uso típico

### Inspeccionar una propiedad BGE

```
[On Update] → [Print: Message = {Get Property: prop="score"}]
```

### Confirmar que un bloque se ejecuta

```
[On Start] → [Print: Message = "init OK"] → [...]
```

### Mostrar estado de FSM

```
[On Update] → [Print: Message = {Get State: FSM ID="player"}]
```

## Notas

- La salida aparece en la consola del sistema (terminal) donde se lanzó Range Game Engine.
- No tiene efecto sobre la lógica del juego ni sobre el rendimiento notable en producción, pero conviene eliminarlo antes de publicar.
- Para imprimir valores numéricos, conéctalos directamente: Python los convierte automáticamente a string.
