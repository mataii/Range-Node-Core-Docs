# Advance Dialogue

**Tipo:** Acción  
**Categoría:** DIALOGUE

Avanza manualmente a la siguiente línea de un diálogo activo. Equivale a que el jugador pulse la tecla de avance, pero puede desencadenarse por cualquier evento.

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
Range.logic.globalDict['_dlg_adv_dlg1'] = True
```

## Uso típico

### Avanzar al hacer clic en cualquier parte de la pantalla

```
# Dialogue Player con Advance=Manual Only
[On Mouse Button: Button=LEFT, Mode=PRESSED]
    → [Advance Dialogue: Dialogue ID="intro"]
```

### Avanzar automáticamente al terminar una animación

```
[On Message: Subject="anim_done"]
    → [Advance Dialogue: Dialogue ID="cutscene"]
```

## Notas

- Funciona tanto si `Dialogue Player` tiene `Advance=Manual Only` como con cualquier otro modo (se combina con la tecla configurada).
- Si el diálogo no está activo, la señal se ignora (`globalDict.pop` la consume sin efecto).
- Usar junto con [Stop Dialogue](stop-dialogue.md) y [On Dialogue End](on-dialogue-end.md) para control completo del flujo.
