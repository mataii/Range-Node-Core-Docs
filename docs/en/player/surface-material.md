# Surface Material

**Type:** Exec (In → Out)  
**Category:** PLAYER

Reads the surface type from the hit object (through a BGE property) and publishes the corresponding FX name and audio name for **Weapon FX System** to use.

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| Property | String | `"_surface"` | BGE property on the hit object that contains the surface type |
| Default Type | String | `"concrete"` | Type used when the property does not exist on the object |

### FX and audio per surface type

Configure an **FX / Audio** pair for each type:

| Type | FX | Audio |
|------|----|-------|
| Metal | (FX object name) | (sound name) |
| Wood | | |
| Concrete | | |
| Dirt | | |
| Flesh | | |
| Glass | | |
| Water | | |
| Default | | |

Empty fields use the default impact FX/audio from Weapon FX System.

## Sockets

| Socket | Direction | Type |
|--------|-----------|------|
| In | Input | Exec |
| Out | Output | Exec |

## Published variables

| Variable | Type | Description |
|----------|------|-------------|
| `surface_type` | string | Surface type of the hit object |
| `surface_fx_name` | string | Name of the corresponding FX object |
| `surface_audio_name` | string | Name of the corresponding sound |

## Graph position

```
[Hit Scan Weapon] → [Surface Material] → [Weapon FX System: Surface FX = True]
```

## Scene object configuration

Add the BGE property `_surface` to each scene object with its corresponding type:

| Object | `_surface` property |
|--------|---------------------|
| Metal walls | `"metal"` |
| Wooden floor | `"wood"` |
| Enemies | `"flesh"` |
| Not configured | uses `Default Type` |
