# Start Dialogue

**Tipo:** Acción  
**Categoría:** DIALOGUE

Inicia una secuencia de diálogo escribiendo una señal en `globalDict`. El nodo [Dialogue Player](dialogue-player.md) con el mismo ID la detectará en el siguiente frame.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Dialogue ID | String | `"dlg1"` | Debe coincidir con el Dialogue Player correspondiente |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
Range.logic.globalDict['_dlg_start_dlg1'] = True
```

## Uso típico

### Iniciar al colisionar con un NPC

```
[On Collision: Object="npc_old_man"]
    → [Start Dialogue: Dialogue ID="intro"]
    → [Overlay Scene: Scene=DialogueHUD, Mode=Add]
```

### Iniciar al pulsar E cerca de un objeto

```
[On Key Press: Key=E, Mode=PRESSED]
    → [Start Dialogue: Dialogue ID="chest_dialogue"]
```

## Notas

- Este nodo solo escribe un flag en `globalDict` — no reproduce nada por sí solo.
- El [Dialogue Player](dialogue-player.md) con el mismo ID es quien controla la reproducción.
- Si el diálogo ya estaba activo cuando se llama a este nodo, se reinicia desde la primera línea.
