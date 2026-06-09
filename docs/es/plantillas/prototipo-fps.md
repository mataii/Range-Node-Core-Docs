# Prototipo FPS

**Tiempo de configuración:** ~5 minutos  
**Nodo principal:** FPS Tools  
**Enfoques cubiertos:** Primera persona, Tercera persona

Esta es una receta — pasos exactos sin explicaciones. Para entender *por qué* funciona cada paso, lee [Mi Primer FPS](../guias/sistemas/mi-primer-fps.md).

---

## Requisitos de la escena

| Objeto | Tipo de física | Nombre | Notas |
|--------|---------------|--------|-------|
| Cápsula (o cualquier mesh) | Character | `player` | El objeto personaje |
| Camera | — | `fps_camera` | Hija de `player`, a nivel de los ojos |
| Plano o Cubo | Static | `floor` | Superficie de suelo |

**Jerarquía de la escena:**
```
player  (física Character)
└── fps_camera  (Camera, hija de player)
```

**Posición de la cámara:** con `fps_camera` seleccionada y `player` como padre, establece Z local ≈ 0.7 (nivel de los ojos dentro de la cápsula).

---

## NodeTree

Con `player` seleccionado:

1. Abre el **Node Editor** → haz clic en **New**
2. `Shift + A` → EVENT → **On Update**
3. `Shift + A` → FPS → **FPS Tools**
4. Conecta: arrastra desde la salida Exec de **On Update** → socket In de **FPS Tools**

```
[On Update] ──► [FPS Tools]
```

**Configuración de FPS Tools:**

| Propiedad | Valor |
|-----------|-------|
| Camera | `fps_camera` |
| Perspective | `First Person` |
| Walk Speed | `5.0` |
| Sprint Speed | `10.0` |
| Jump Force | `7.0` |
| Health | `True`, Max=`100` |
| Ammo | `True`, Max=`30` |

---

## Registro del componente

1. Panel lateral del Node Editor (`N` → pestaña **RNC**) → haz clic en **Info Componente**
2. Editor de Propiedades → pestaña **Game** → **Game Components**:
   - Elimina el placeholder `module` si existe
   - Haz clic en **Add RNC Component** → pega → confirma
   - Haz clic en **Assign NodeTree**

---

## Ejecutar

Presiona `P` sobre el viewport 3D.

**Controles:**  
`WASD` — mover | `Left Shift` — correr | `Space` — saltar | `Ratón` — mirar

---

## Variante en Tercera Persona

1. En FPS Tools, cambia **Perspective** a `Third Person`
2. Establece **Distance** a `4.0` (desplazamiento de la cámara respecto al personaje)
3. La cámara ya no necesita ser hija de `player` — FPS Tools gestiona su posición automáticamente. Puedes eliminar la relación de padre (`Alt + P`) si lo deseas.

**Los controles permanecen iguales.** La cámara sigue al personaje desde atrás y arriba.

---

## Variante modular

Para un pipeline modular (Player Input + Player Movement + Player Jump + Mouse Look) con el mismo resultado pero control independiente de cada nodo, sigue [Mi Primer FPS — Método B](../guias/sistemas/mi-primer-fps.md#paso-1--crear-el-nodetree-1).

---

## Siguientes pasos

- Añadir un enemigo → [Guía de IA del enemigo](../guias/sistemas/ia-enemigo.md)
- Tutorial completo con explicaciones → [Mi Primer FPS](../guias/sistemas/mi-primer-fps.md)
- Referencia: [FPS Tools](../fps/fps-tools.md), [categoría PLAYER](../player/index.md)
