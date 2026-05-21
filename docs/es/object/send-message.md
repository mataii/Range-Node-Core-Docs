# Send Message

**Tipo:** Acción  
**Categoría:** OBJECT

Envía un mensaje BGE usando el sistema de mensajería nativo de Range Game Engine (`sendMessage`). Diferente al bus de mensajes RNC (`Node Broadcast`).

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Subject | String | `"msg"` | Asunto del mensaje |
| Body | String | `""` | Cuerpo del mensaje |
| To | String | `""` | Objeto destinatario. Vacío = broadcast a todos |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
self.own.sendMessage('event', '', '')  # broadcast
self.own.sendMessage('event', 'data', 'target_obj')  # a destino específico
```

## Diferencia con Node Broadcast

| Send Message (BGE nativo) | Node Broadcast (RNC) |
|---------------------------|----------------------|
| Usa el sistema BGE nativo | Usa `globalDict` |
| Compatible con sensores `Message` de BGE | Solo funciona con nodos RNC |
| Puede enviar a objetos específicos | Broadcast global a todos los nodos RNC |
| No disponible en todas las versiones de Range | Siempre disponible en RNC |

## Notas

- Preferir [Node Broadcast](../event/node-broadcast.md) para comunicación entre nodos RNC.
- Usar `Send Message` cuando necesitas interoperabilidad con sensores/actuadores BGE nativos.
