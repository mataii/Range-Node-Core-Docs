# Trigger Camera Shake

**Tipo:** Acción  
**Categoría:** CAMERA

Activa un efecto de temblor de cámara escribiendo los parámetros en `globalDict`. Requiere que el nodo [Camera Shake](camera-shake.md) esté ejecutándose en la cámara.

## Propiedades

| Propiedad | Tipo | Por defecto | Descripción |
|-----------|------|-------------|-------------|
| Intensity | Float | `0.3` | Amplitud máxima del desplazamiento |
| Frames | Int | `20` | Duración del temblor en frames |

## Sockets

| Socket | Dirección | Tipo |
|--------|-----------|------|
| In | Entrada | Exec |
| Out | Salida | Exec |

## Código generado

```python
Range.logic.globalDict['_rnc_shk'] = {'intensity': 0.3, 'frames': 20, 'current': 0}
```

## Uso típico

### Temblor al recibir daño

```
[On Message: Subject="take_damage"] → [Trigger Camera Shake: Intensity=0.2, Frames=15]
```

### Temblor de explosión

```
[On Collision: Property="explosive"] → [Trigger Camera Shake: Intensity=0.8, Frames=40]
```

### Temblor de impacto de arma

```
[Weapon Fire Executor] → [Trigger Camera Shake: Intensity=0.1, Frames=5]
```

## Notas

- Este nodo **solo activa** el temblor. El objeto cámara necesita el nodo [Camera Shake](camera-shake.md) ejecutándose cada frame para aplicar el efecto visualmente.
- Si se activa mientras ya hay un temblor activo, los parámetros se sobreescriben con los nuevos valores.
