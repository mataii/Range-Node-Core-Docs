# Interaction System

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

Sistema de interacción con objetos de la escena mediante raycast. Detecta objetos interactuables al presionar (o mantener) una tecla, y opcionalmente les envía un mensaje BGE o modifica una propiedad.

## Propiedades

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| Range | Float | `2.5` | Distancia máxima de interacción (unidades de Range) |
| Property Filter | String | `"interactable"` | Propiedad BGE que marca objetos como interactuables |
| Type Property | String | `"interaction_type"` | Propiedad BGE de la que se lee el tipo de interacción |
| Interact Key | Enum | `E` | Tecla de interacción |
| Cooldown | Float | `0.3` | Segundos mínimos entre interacciones consecutivas |
| Origin | Enum | `Camera` | Origen del raycast (`Camera` para FPS, `Object` para IA/tercera persona) |
| Hold to Interact | Bool | `False` | Requiere mantener la tecla durante `Hold Duration` segundos |
| Hold Duration | Float | `1.0` | Segundos que hay que mantener la tecla |
| Auto Pickup | Bool | `False` | Interactúa automáticamente con objetos de tipo `Auto Type` |
| Auto Type | String | `"pickup"` | Tipo de interacción para auto-pickup |
| Send Message | Bool | `True` | Llama a `target.sendMessage()` al interactuar |
| Message | String | `""` | Asunto del mensaje (vacío = usa el valor de `interaction_type`) |
| Set Property | Bool | `False` | Asigna `target['_int_triggered'] = 1` al interactuar |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables publicadas

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `interaction_available` | bool | `True` cuando hay un objeto interactuable en rango |
| `interaction_object` | KX_GameObject | Objeto detectado (o `None`) |
| `interaction_type` | string | Valor de la propiedad `Type Property` del objeto |
| `interaction_triggered` | bool | Pulso de un frame al completarse la interacción |
| `interaction_hold_progress` | float (0–1) | Progreso del hold (solo en modo Hold to Interact) |

## Configuración de objetos interactuables

Añade a cada objeto de la escena que quieras que sea interactuable:

1. Propiedad BGE `interactable` (o la que configures en Property Filter)
2. Propiedad BGE `interaction_type` con el tipo: `"pickup"`, `"door"`, `"lever"`, etc.

## Uso típico

```
[OnUpdate] → [Interaction System] → [Out]
                 ↓ interaction_available
            [Branch] ──► True ──► [UI: Mostrar "Presiona E"]
```

El mensaje BGE enviado al objeto interactuable puede activar su propia lógica sin necesidad de referencias directas.
