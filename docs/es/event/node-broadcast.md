# Node Broadcast

**Tipo:** Exec (In → Out)  
**Categoría:** EVENT

Emisor del bus de mensajes interno de RNC. Envía un mensaje con un `subject` y datos opcionales a todos los nodos [On Message](on-message.md) que escuchen ese mismo subject.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Subject | String | `"my_event"` | Identificador del mensaje a enviar |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Data | Entrada | Data (cualquier tipo) |
| Out | Salida | Exec |

## Comportamiento

```python
Range.logic.globalDict['rnc_msg_<subject>']      = True
Range.logic.globalDict['rnc_msg_<subject>_data'] = <data>
```

El mensaje es un flag booleano en `globalDict`. Los receptores (`On Message`) lo leen y lo consumen en el mismo frame.

### Socket Data

Acepta cualquier expresión Python. Si no está conectado, el valor por defecto es `None`. Los receptores lo leen como `_msg_data`.

Tipos válidos para Data:
- Literales: `42`, `"hola"`, `True`, `[1, 2, 3]`
- Expresiones: `self._bt_bb.get('score', 0)`, `self.own.worldPosition.copy()`
- `None` (sin datos)

## Uso típico

### Evento simple sin datos

```
[BT Condition: hp <= 0]
    └── True ──► [Node Broadcast: subject="player_died"]
```

### Evento con datos adjuntos

```
# Enviar la posición del jugador al morir
[Node Broadcast: subject="player_died",
                 Data = self.own.worldPosition.copy()]
```

### Pipeline de notificación

```
[On Update] → [Enemy Combat]
                    → [BT Condition: ai_attack_requested]
                              └── True ──► [Node Broadcast: subject="enemy_attacked"]
```

### Múltiples receptores del mismo subject

```
# Objeto Manager:
[On Update] → [On Message: "enemy_attacked"] → [actualizar contador]

# Objeto HUD:
[On Update] → [On Message: "enemy_attacked"] → [mostrar indicador]

# Objeto Audio:
[On Update] → [On Message: "enemy_attacked"] → [reproducir sonido]
```

Todos los objetos con `On Message: "enemy_attacked"` recibirán el broadcast.

## Cuándo usar vs alternativas

| Situación | Solución recomendada |
|-----------|---------------------|
| Notificar a un objeto específico | `globalDict['_dmg_' + obj.name]` (canal directo) |
| Notificar a todos sin saber cuántos | **Node Broadcast** |
| Comunicación en el mismo objeto | Variables `self.*` directas |
| Comunicar datos complejos entre escenas | `globalDict` manual |

## Notas

- Node Broadcast escribe el flag **cada frame que se ejecuta** — si está conectado directamente a OnUpdate sin condición, enviará el mensaje en cada frame.
- El flag persiste en `globalDict` hasta que `On Message` lo consume. Si ningún objeto tiene ese receptor activo, el flag queda `True` hasta que algo lo lea o limpie.
- Para limpiar un mensaje manualmente: `Range.logic.globalDict.pop('rnc_msg_<subject>', None)`.
- Node Broadcast se ejecuta secuencialmente como cualquier nodo Exec — el mensaje estará disponible en `globalDict` en el **mismo frame**, para que receptores en otros objetos que ejecuten después lo lean.
