# On Message

**Tipo:** Branch (Received / None)  
**Categoría:** EVENT

Receptor del bus de mensajes interno de RNC. Espera un mensaje con un `subject` específico enviado por [Node Broadcast](node-broadcast.md). Al recibirlo, ejecuta `Received` y expone los datos adjuntos.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Subject | String | `"my_event"` | Identificador del mensaje a escuchar |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Received | Salida | Exec (mensaje recibido este frame) |
| None | Salida | Exec (sin mensaje) |
| Data | Salida | Data (cualquier tipo) |

## Comportamiento

```python
# Clave en globalDict: "rnc_msg_<subject>"
if Range.logic.globalDict.get('rnc_msg_my_event', False):
    _msg_data = Range.logic.globalDict.get('rnc_msg_my_event_data', None)
    Range.logic.globalDict['rnc_msg_my_event'] = False  # consumir
    # → Received
else:
    # → None
```

El mensaje se **consume** al leerlo: el flag se pone a `False` para evitar que el mismo mensaje dispare en múltiples frames.

### Claves de globalDict usadas

| Clave | Contenido |
|-------|-----------|
| `rnc_msg_<subject>` | Bool — True cuando hay un mensaje pendiente |
| `rnc_msg_<subject>_data` | Cualquier valor enviado por Node Broadcast |

## Uso típico

### Recibir evento de muerte

```
# Objeto A (enemigo) con Node Broadcast:
[On Update] → [BT Condition: ai_is_dead]
                  └── True ──► [Node Broadcast: subject="enemy_died"]

# Objeto B (manager de ola) con On Message:
[On Update] → [On Message: subject="enemy_died"]
                  └── Received ──► [BTCustomTask: wave_manager.enemy_count -= 1]
```

### Recibir datos con el mensaje

```
# Emisor:
[Node Broadcast: subject="player_scored", Data = self._bt_bb.get('score_delta', 0)]

# Receptor:
[On Update] → [On Message: subject="player_scored"]
                  └── Received ──► [BTCustomTask:
                                       total = Range.logic.globalDict.get('total_score', 0)
                                       Range.logic.globalDict['total_score'] = total + _msg_data]
```

### Comunicación entre escenas

Node Broadcast / On Message usan `globalDict`, que persiste entre escenas (si no se limpia):

```
# Escena A envía:
[Node Broadcast: subject="cutscene_done"]

# Escena B recibe (si carga antes de que expire el flag):
[On Start] → [On Message: subject="cutscene_done"]
```

## Diferencia con `sendMessage()` nativo de Range

| On Message / Node Broadcast | sendMessage() nativo |
|-----------------------------|---------------------|
| Bus interno de RNC vía globalDict | Sistema de Logic Bricks |
| Funciona entre componentes Python | Funciona entre sensores/actuadores |
| Datos adjuntos (cualquier tipo) | Solo subject y body (strings) |
| No compatible con Message Sensor | No compatible con On Message |

## Notas

- Si varios objetos tienen On Message con el mismo `subject`, **todos** recibirán el mensaje (el flag es global y se consume en el primer receptor que ejecute ese frame). Para mensajes dirigidos, incluir el nombre del destinatario en el subject: `"msg_Enemy_01"`.
- El flag se consume en el mismo frame en que se recibe. Si On Message no ejecuta ese frame (porque el objeto está fuera de cámara o el árbol no corre), el mensaje se pierde.
- `_msg_data` es la variable local disponible en los nodos siguientes al recibir el mensaje.
