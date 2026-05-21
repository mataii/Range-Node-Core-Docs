# Set Text

**Tipo:** Acción  
**Categoría:** UI

Establece el texto de un objeto de fuente (`KX_FontObject`) en la escena. Acepta un valor dinámico de datos o texto estático.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Object | Enum | — | Objeto de texto en la escena |
| Text | String | `""` | Texto estático (usado si el socket Text no está conectado) |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Text | Entrada | Dato (String) |
| Out | Salida | Exec |

## Código generado

```python
_txt_obj = self.own.scene.objects.get('score_text')
if _txt_obj and hasattr(_txt_obj, 'text'):
    _txt_obj.text = str(42)
```

## Uso típico

### Velocímetro

```
[On Update]
    → [Set Text:
           Object=speed_label
           Text={Number To String:
                     Value={Get Velocity: Component=Speed XY}
                     Decimals=1
                     Suffix=" km/h"}]
```

### Puntuación

```
[On Message: Subject="score_update"]
    → [Set Text:
           Object=score_label
           Text={Concat String:
                     A="Score: "
                     B={Get Property: prop="score"}}]
```

### Texto de diálogo dinámico

```
[On Update]
    → [Set Text: Object=dialogue_box, Text={Get Dialogue Text}]
```

## Notas

- El objeto debe ser de tipo **Text** (fuente) en Range Game Engine — no un objeto Mesh.
- El valor se convierte a `str()` automáticamente, por lo que puedes conectar cualquier tipo de dato.
- El objeto puede estar en la escena principal o en una escena overlay de HUD.
