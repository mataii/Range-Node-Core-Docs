# FPS Prototype

**Setup time:** ~5 minutes  
**Main node:** FPS Tools  
**Approaches covered:** First Person, Third Person

This is a recipe — exact steps with no explanation. To understand *why* each step works, read [My First FPS](../guides/systems/my-first-fps.md).

---

## Scene requirements

| Object | Physics type | Name | Notes |
|--------|-------------|------|-------|
| Capsule (or any mesh) | Character | `player` | The character object |
| Camera | — | `fps_camera` | Child of `player`, at eye level |
| Plane or Cube | Static | `floor` | Ground surface |

**Scene hierarchy:**
```
player  (Character physics)
└── fps_camera  (Camera, child of player)
```

**Camera position:** with `fps_camera` selected and `player` as parent, set local Z ≈ 0.7 (eye level inside the capsule).

---

## NodeTree

With `player` selected:

1. Open **Node Editor** → click **New**
2. `Shift + A` → EVENT → **On Update**
3. `Shift + A` → FPS → **FPS Tools**
4. Connect: drag from **On Update** Exec output → **FPS Tools** In socket

```
[On Update] ──► [FPS Tools]
```

**FPS Tools configuration:**

| Property | Value |
|----------|-------|
| Camera | `fps_camera` |
| Perspective | `First Person` |
| Walk Speed | `5.0` |
| Sprint Speed | `10.0` |
| Jump Force | `7.0` |
| Health | `True`, Max=`100` |
| Ammo | `True`, Max=`30` |

---

## Component registration

1. Node Editor side panel (`N` → **RNC** tab) → click **Info Componente**
2. Properties Editor → **Game** tab → **Game Components**:
   - Remove the `module` placeholder if present
   - Click **Add RNC Component** → paste → confirm
   - Click **Assign NodeTree**

---

## Run

Press `P` over the 3D viewport.

**Controls:**  
`WASD` — move | `Left Shift` — sprint | `Space` — jump | `Mouse` — look

---

## Third Person variant

1. In FPS Tools, change **Perspective** to `Third Person`
2. Set **Distance** to `4.0` (camera offset from character)
3. The camera no longer needs to be a child of `player` — FPS Tools handles its position automatically. You can remove the parent relationship (`Alt + P`) if desired.

**Controls remain the same.** The camera follows the character from behind and above.

---

## Modular variant

For a modular pipeline (Player Input + Player Movement + Player Jump + Mouse Look) with the same result but independent node control, follow [My First FPS — Method B](../guides/systems/my-first-fps.md#method-b--modular-pipeline).

---

## Next steps

- Add an enemy → [Enemy AI guide](../guides/systems/enemy-ai.md)
- Full tutorial with explanations → [My First FPS](../guides/systems/my-first-fps.md)
- Reference: [FPS Tools](../fps/fps-tools.md), [PLAYER category](../player/index.md)
