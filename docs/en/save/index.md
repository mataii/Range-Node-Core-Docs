# SAVE

Nodes for managing persistent data in Range Game Engine. Data is stored in `Range.logic.globalDict` (memory) and can be persisted to disk via Save Game / Load Game.

## Nodes

| Node | Type | Description |
|------|------|-------------|
| [Set Save Data](set-save-data.md) | Action | Write a value to globalDict under a key |
| [Get Save Data](get-save-data.md) | Data | Read a value from globalDict by key |
| [Has Save Data](has-save-data.md) | Action (branch) | Check whether a key exists in globalDict |
| [Delete Save Data](delete-save-data.md) | Action | Remove a key from globalDict |
| [Save Game](save-game.md) | Action (branch) | Persist globalDict to a file on disk |
| [Load Game](load-game.md) | Action (branch) | Load a save file into globalDict |
| [Save Exists](save-exists.md) | Action (branch) | Check whether a save file exists |
| [Property To Save](copy-property-to-save.md) | Action | Copy a BGE property into globalDict |
| [Property From Save](copy-property-from-save.md) | Action | Copy a globalDict value into a BGE property |
| [Clear Save Data](clear-save-data.md) | Action | Erase all contents of globalDict |

## Typical save flow

```
[On Key Press: Key=F5, Mode=PRESSED]
    → [Property To Save: Property=health, Save Key="health"]
    → [Property To Save: Property=score,  Save Key="score"]
    → [Save Game: Filename="savegame", Slot=0]
          └── Saved ──► [Set Text: Text="Saved!"]
          └── Error ──► [Set Text: Text="Save failed"]
```

## Notes

- `globalDict` is shared by all objects in the session — it acts as global memory.
- Calling `Save Game` writes the entire contents of `globalDict` to disk.
- Calling `Load Game` replaces the current `globalDict` contents with those from the file.
