# UnderDeskCableTray

CornerCAD commercial design: a parametric 150 mm J-hook cable channel that screws under a desk.

- Two countersunk #8 wood screw holes, driven from below outside the cord pocket.
- A full-length **edge guide lip** hugs the desk edge, so a row of channels lines up square and parallel with no measuring.
- Pocket about 40 × 30 mm for 6–8 power cords and USB leads; the entry gap faces into the desk so cords push up and in.
- Prints on end on the Creality K2 Plus with **designed-in snap-off feet**; no slicer supports.
- "CornerCAD" embossed on the outer wall.

## Status

Plan v3 approved 2026-10-22. Model built 2026-10-23 (`macros/01-build_channel.FCMacro`), audit clean; awaiting review. Previews: `images/channel-v3-preview*.png`.

## Layout

| Path | Contents |
|---|---|
| `intent.md` | Goal and constraints |
| `plan.md` | Parameters, feature tree, validation plan |
| `Params.FCStd` | VarSet (created by `macros/00-create_params.FCMacro`) |
| `macros/` | FreeCAD macros |
| `scripts/audit_parametric.py` | Parametric audit — must be clean before commit |
| `stl/`, `3mf/` | Print deliverables |
| `images/` | Renders |
