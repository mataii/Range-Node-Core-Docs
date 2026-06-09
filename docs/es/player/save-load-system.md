# Save Load System

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

Guarda y carga el estado del juego en archivos JSON. Soporta múltiples slots, teclas configurables, autosave por tiempo y carga automática al inicio. Puede persistir salud del jugador, munición de armas y propiedades de objetos del mundo.

## Propiedades

### Archivo

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Path | String | `"//saves/"` | Directorio de archivos de guardado (`//` = carpeta del juego) |
| Slot | Int (0–9) | `0` | Índice del slot de guardado activo |
| Version | Int (≥1) | `1` | Versión del formato de guardado; archivos con versión diferente son rechazados |

### Teclas

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Save Key | Enum | `T` | Tecla para guardar manualmente |
| Load Key | Enum | `B` | Tecla para cargar manualmente |

### Inicio y autosave

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Load on Start | Bool | `False` | Carga el slot automáticamente en el primer frame si existe |
| Autosave | Bool | `False` | Activa el guardado automático por tiempo |
| Autosave Rate | Float (≥5) | `60.0` | Segundos entre guardados automáticos |

### Contenido del guardado

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Player State | Bool | `True` | Incluye `player_hp` y `player_hp_max` |
| Ammo State | Bool | `True` | Incluye `weapon_ammo_in_mag`, `weapon_magazines`, `weapon_type` |
| World Objects | Bool | `False` | Guarda propiedades `sl_*` de objetos con la propiedad BGE `_sl_id` |

### Debug

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Debug | Bool | `False` | Imprime eventos de guardado/carga en la consola |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables publicadas

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `save_slot` | int | Slot activo |
| `save_exists` | bool | `True` si existe un archivo en el slot actual |
| `save_success` | bool | Pulso de un frame tras guardado o carga exitosos |
| `save_loading` | bool | `True` durante el frame en que se ejecuta una carga |

## Formato del archivo

Los archivos se guardan como `slot_<N>.json` dentro del directorio configurado en `Path`. Ejemplo con todas las opciones activas:

```json
{
  "_version": 1,
  "player_hp": 75.0,
  "player_hp_max": 100.0,
  "weapon_ammo_in_mag": 12,
  "weapon_magazines": 3,
  "weapon_type": "rifle",
  "world": {
    "42": { "sl_open": true },
    "17": { "sl_collected": 1 }
  }
}
```

## Guardado de objetos del mundo

Cuando `World Objects = True`, el nodo busca en la escena todos los objetos que tengan la propiedad BGE `_sl_id` y guarda todas sus propiedades que empiecen por `sl_`. Al cargar, restaura esas propiedades sobre los objetos con el `_sl_id` correspondiente.

```
Objeto en escena:
  _sl_id = 42          (identificador único)
  sl_open = False      (se guarda y restaura)
  sl_collected = 0     (se guarda y restaura)
```

## Integración con otros nodos

SaveLoadSystem **no escribe directamente** en variables de gameplay. Publica los datos en propiedades BGE del objeto propietario (`self.own['_sl_*']`), y los nodos de gameplay (Player Health, Inventory Manager) los leen en su frame de inicialización para restaurar su estado:

| Propiedad BGE | Consumida por |
|---------------|---------------|
| `_sl_player_hp` | Player Health |
| `_sl_player_hp_max` | Player Health |
| `_sl_inventory` | Inventory Manager |
| `_sl_weapon_state` | Weapon State Publisher |
| `_sl_loaded` | (flag de carga completada) |

## Posición en el grafo

```
[OnUpdate] → [Save Load System] → [Player Health] → [Inventory Manager] → ...
```

Debe ir **antes** de los nodos que consumen sus datos de restauración.

## Notas

- El `Version` funciona como protección: si el archivo guardado tiene una versión distinta a la configurada, se rechaza y no se carga.
- `save_success` es un pulso de un frame — úsalo en un nodo Branch para mostrar feedback al jugador.
- Los archivos se crean con `os.makedirs(..., exist_ok=True)`, por lo que el directorio se crea automáticamente si no existe.
