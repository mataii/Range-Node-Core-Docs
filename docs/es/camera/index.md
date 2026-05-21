# Categoría CAMERA

Nodos para controlar cámaras: seguimiento suave, campo de visión, temblor de cámara y selección de cámara activa.

## Nodos disponibles

| Nodo | Tipo | Descripción |
|------|------|-------------|
| [Set Camera](set-camera.md) | Acción | Cambia la cámara activa de la escena |
| [Set FOV](set-fov.md) | Acción | Cambia el campo de visión (FOV) de una cámara |
| [Camera Follow](camera-follow.md) | Acción | Mueve la cámara suavemente hacia un objetivo |
| [Trigger Camera Shake](trigger-camera-shake.md) | Acción | Activa un efecto de temblor de cámara |
| [Camera Shake](camera-shake.md) | Procesador | Procesa el temblor frame a frame en la cámara |

## Patrón de temblor de cámara

`Trigger Camera Shake` escribe los parámetros de temblor en `globalDict`. El nodo `Camera Shake` debe ejecutarse en el objeto cámara cada frame para procesar y aplicar el desplazamiento aleatorio con fade.

```
# En el objeto que dispara el evento:
[On Collision: Property="explosive"] → [Trigger Camera Shake: Intensity=0.5, Frames=30]

# En la cámara (ejecutando cada frame):
[On Update] → [Camera Shake]
```
