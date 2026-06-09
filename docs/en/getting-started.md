# Getting Started

This guide takes you from zero to your first working game object with visual
logic in Range Game Engine. You will build a cube that rotates continuously,
without writing a single line of Python code.

!!! info "Addon under active development"
    Range Node Core is under active development. Workflows, button names, or
    setup steps may change in future versions. This guide reflects the behavior
    of the current release.

---

## What you will build

A cube that rotates on its Z axis every frame, using just two nodes:

| Node | Category | Role |
|------|----------|------|
| **On Update** | EVENT | Entry point — runs every frame |
| **Transform** | OBJECT | Applies the rotation to the object |

---

## Prerequisites

- Blender 2.79 with Range Game Engine installed
- The **Range Node Core** addon enabled
- A scene with at least one mesh object (the default cube works fine)

---

## Step 1 — Enable the addon

Go to **File → User Preferences → Add-ons**, search for **Range Node Core**,
and enable it.

If it is already enabled, continue to step 2.

<!-- screenshot: Add-ons panel with Range Node Core enabled and checkbox checked -->

---

## Step 2 — Open the Node Editor

Switch one of your editors to **Node Editor**.

!!! warning "Select the correct type"
    The Node Editor header has a type selector that defaults to *Shader Nodes*
    or *Compositor*. Change it to **Range Node Core**. If this option is not
    visible, the addon is not correctly enabled.

<!-- screenshot: Node Editor header with the type selector showing "Range Node Core" -->

---

## Step 3 — Select the object

In the 3D viewport, select the object you want to add logic to.
This guide uses the default cube.

The Node Editor will show the NodeTree linked to the selected object,
or remain empty if none has been created yet.

---

## Step 4 — Create a NodeTree

With the object selected, click the **New** button in the Node Editor header.

This creates an empty NodeTree and associates it with the active object's data.
The NodeTree stores the visual node graph, but on its own it does not execute
anything during gameplay — for that you need to register it as a component,
which you will do later in this guide.

You can rename the NodeTree by double-clicking the name field in the header.

<!-- screenshot: "New" button in the Node Editor header, and the empty canvas
     after clicking it, with the NodeTree name visible -->

---

## Step 5 — Add the On Update node

Press `Shift + A` to open the Add menu and navigate to **EVENT → On Update**.
Click to place it on the canvas.

**On Update** is the entry point of the tree. Its `Exec` output socket fires
the connected logic every frame, as long as the object exists in the scene.

<!-- screenshot: On Update node on the canvas, showing its Exec output socket -->

---

## Step 6 — Add the Transform node

Press `Shift + A` again and navigate to **OBJECT → Transform**.
Place it to the right of the On Update node.

---

## Step 7 — Configure the Transform node

Select the Transform node and set its properties:

| Property | Value |
|----------|-------|
| Mode | `ROTATE` |
| X | `0.0` |
| Y | `0.0` |
| Z | `0.3` |
| Local | `True` |

!!! note "Radians, not degrees"
    The value `Z = 0.3` is in **radians per frame**. At 60 FPS this is
    roughly one full rotation every 3.5 seconds.  
    Conversion formula: `degrees × π ÷ 180`  
    Example: 5° per frame → `5 × 3.14159 ÷ 180 ≈ 0.087`

<!-- screenshot: Transform node with Mode=ROTATE and Z=0.3 configured -->

---

## Step 8 — Connect the nodes

Drag from the **Exec** socket (output) of **On Update** to the **In** socket
(input) of **Transform**.

The graph should look like this:

```
[On Update] ──Exec──► [Transform: ROTATE Z=0.3]
```

<!-- screenshot: both nodes connected with the Exec wire visible between them -->

---

## Register the component

The NodeTree you just built stores the visual logic, but it is not yet connected
to the game's execution system. For Range to run it during gameplay, the NodeTree
must be linked to a **Game Component** on the object.

The component RNC registers is:

```
range_node_core.runtime.rnc_component.RNC_Component
```

This identifier tells Range which Python class to instantiate as the object's
component. You do not normally need to type it: the **Component Info** button
copies it to your clipboard automatically.

This process has two parts: creating the component with the correct identification,
then explicitly assigning the NodeTree to it.

!!! note "Why is this step manual?"
    When you run the game, Range needs to know it should instantiate
    `range_node_core.runtime.rnc_component.RNC_Component` as this object's
    component. Range's Game Components system requires that registration to
    exist before the simulation starts.

    The process is manual because Range does not expose a public API that
    allows registering a `KX_PythonComponent` from Python at edit time.
    The **Component Info** button exists as a practical workaround: it
    generates the correct registration string so you can paste it without
    knowing the module's internal path. In future versions of the addon,
    this step may be automated if Range exposes that API.

### 1. Copy the component info

You can access the **Component Info** button in two places:

**Option A — From the Node Editor side panel:**

1. Press `N` to open the side panel
2. Under the **RNC** tab, click **Component Info**

**Option B — From the On Update node:**

1. Select the On Update node on the canvas
2. In the node properties panel, click **Component Info**

Both options copy the identifier
`range_node_core.runtime.rnc_component.RNC_Component` to your clipboard.
This is the Python component RNC uses to execute the NodeTree. You do not
need to type it manually — just paste it in the next step.

<!-- screenshot: side panel with the RNC tab and "Component Info" button visible -->

### 2. Go to the object's Game Properties

Switch to the **Properties Editor** and select the **Game** tab
(game controller icon).

Find the **Game Components** section within that panel.

<!-- screenshot: Properties Editor with the Game tab active,
     Game Components section visible -->

### 3. Remove the "module" component if present

If the Game Components list contains an entry named `module`, select it
and delete it before continuing.

!!! note "Why does that component exist?"
    Range sometimes adds a placeholder component named `module` automatically.
    Leaving it in place may conflict with the RNC component you are about
    to create.

<!-- screenshot: "module" component selected in the list,
     with the delete button visible -->

### 4. Add the RNC component

Click **Add RNC Component** in the Game Components section.

A popup window will appear with a text field. Paste the text you copied
(`Ctrl + V` on Windows/Linux, `Cmd + V` on Mac). The field should read:

```
range_node_core.runtime.rnc_component.RNC_Component
```

Click **OK** to confirm.

<!-- screenshot: popup window with the string already pasted in the field,
     before confirming with OK -->

### 5. Assign the NodeTree

After confirming, the new component appears in the Game Components list,
but it does not yet know which NodeTree it should execute.

Click **Assign NodeTree**.

This links the component to whichever NodeTree is currently open in the
Node Editor. From this point on, every time you run the game, Range will
compile that node graph and execute it as part of the component.

!!! warning "Do not skip this step"
    If you skip Assign NodeTree, the component has no logic to execute and
    the cube will not rotate. There is no error message — nothing will happen.

<!-- screenshot: "Assign NodeTree" button visible on the component entry
     inside the Game Components section -->

<!-- composite screenshot suggestion: a row of four images showing the full
     registration flow:
     (1) "Component Info" button in the side panel,
     (2) popup window with range_node_core.runtime.rnc_component.RNC_Component pasted,
     (3) newly created component in the Game Components list,
     (4) final state after clicking "Assign NodeTree", showing the NodeTree
         name linked to the component in the panel -->

---

## Step 9 — Run the game

Place your cursor over the 3D viewport and press **P** (or click the **Start**
button in the Game Engine header if visible).

**Expected result:** the cube starts rotating continuously on its Z axis.

Press **Escape** to stop the simulation.

<!-- screenshot: cube rotating during Game Engine playback -->

---

## What you learned

You completed the core RNC workflow:

1. **Build logic visually** — nodes connected in the Node Editor
2. **Register the component** — copying the info, adding the component,
   and assigning the NodeTree connects the graph to the execution system
3. **Run** — Range compiles the graph and executes it every frame

This same workflow applies to any logic, from player movement to complex
AI and combat systems.

---

## Next steps

- [On Update](event/on-update.md) — full reference for the entry point node
- [Transform](object/transform.md) — full reference for the transform node
- [On Key Press](event/on-key-press.md) — respond to player input
- [Branch](logic/branch.md) — conditional logic

---

## Frequently asked questions

**Do I have to repeat the registration process every time I change the graph?**

No. Once the component is registered and the NodeTree is assigned, any changes
you make to the nodes compile automatically the next time you run the game.
You only need to repeat the process if you rename the NodeTree or create a new one.

**The cube is not rotating. What could have gone wrong?**

Check in this order:

1. Is the Exec socket of On Update connected to the In socket of Transform?
2. Did you click **Assign NodeTree** after adding the component?
3. Did you remove the `module` component if it was present?
4. Is the Node Editor type selector set to Range Node Core?
