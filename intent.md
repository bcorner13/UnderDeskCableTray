# Intent — UnderDeskCableTray

## Goal

A simple, parametric under-desk **cable channel** that screws to the underside of a desk
and keeps computer power cords and USB leads tidy and off the floor. Several short
channels mount in a row along the desk edge. The power strip itself is not held by this
part: it screws to the desk through its own keyhole slots.

## Requirements (from Bradley, 2026-10-22)

- **Commercial design**: original CornerCAD product, sold as prints/STLs.
- **Simple design** with **screw holes** for mounting to the desk underside (#8 wood
  screws).
- **Edge guide** that lines the channel up along the length of the desk edge, so it
  installs square and parallel without measuring.
- **Short channels: 150 mm long.**
- **J-hook profile**, entry gap facing into the desk; cords push up and in from below.
- **Capacity: 6–8 cords** (standard computer power cords: IEC C13/C5, NEMA 5-15, plus USB).
- **"CornerCAD" embossed.**
- **Printed at an appropriate angle with designed-in supports**: on end, with breakaway
  feet modeled into the print file.
- Printed on the Creality K2 Plus.

## Constraints

- Must follow `CAD_STANDARDS.md` (mm, watertight/manifold, centered at origin,
  2.0–3.0 mm nominal wall).
- Must follow the parametric rules in `CLAUDE.md` and `~/.claude/CLAUDE.md`: every
  dimension bound to `<<Params>>#VarSet.*`, no literal numbers in constraints.
- No slicer supports needed; the designed-in feet snap off by hand.
- No hardware other than the wood screws that mount it.

## Success criteria

- `python3 scripts/audit_parametric.py` reports zero issues.
- Changing `ChannelLength`, `PocketWidth`, `PocketHeight` or `EntryGap` rebuilds the whole
  part, feet included, without a recompute error.
- The feet snap off cleanly, the channel screws up flush and square to the desk edge, and
  6–8 cords push in through the gap and stay put.
