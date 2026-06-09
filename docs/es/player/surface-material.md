# Surface Material

**Tipo:** Exec (In → Out)  
**Categoría:** PLAYER

Lee el tipo de superficie del objeto impactado (a través de una propiedad BGE) y publica el nombre del FX y del audio correspondiente para que **Weapon FX System** los use.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|---------|-------------|
| Property | String | `"_surface"` | Propiedad BGE del objeto impactado que contiene el tipo de superficie |
| Default Type | String | `"concrete"` | Tipo usado cuando la propiedad no existe en el objeto |

### FX y Audio por tipo de superficie

Configura un par **FX / Audio** para cada tipo:

| Tipo | FX | Audio |
|------|----|-------|
| Metal | (nombre del objeto FX) | (nombre del sonido) |
| Wood | | |
| Concrete | | |
| Dirt | | |
| Flesh | | |
| Glass | | |
| Water | | |
| Default | | |

Los campos vacíos usan el FX/audio de impacto por defecto de Weapon FX System.

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Variables publicadas

| Variable | Tipo | Descripción |
|----------|------|-------------|
| `surface_type` | string | Tipo de superficie del objeto impactado |
| `surface_fx_name` | string | Nombre del objeto FX correspondiente |
| `surface_audio_name` | string | Nombre del sonido correspondiente |

## Posición en el grafo

```
[Hit Scan Weapon] → [Surface Material] → [Weapon FX System: Surface FX = True]
```

## Configuración en los objetos de la escena

Añade la propiedad BGE `_surface` a cada objeto del escenario con el tipo correspondiente:

| Objeto | Propiedad `_surface` |
|--------|----------------------|
| Paredes de metal | `"metal"` |
| Suelo de madera | `"wood"` |
| Enemigos | `"flesh"` |
| Sin configurar | usa `Default Type` |
