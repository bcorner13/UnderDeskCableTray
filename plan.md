# Plan — UnderDeskCableTray

**Status:** v3 **APPROVED** 2026-10-22 and **BUILT** 2026-10-23 by `macros/01-build_channel.FCMacro`
in a sandboxed headless FreeCAD 1.1.0, **not verified on Bradley's laptop** (see VALIDATION). The feature tree below is **as built**; where it differs
from the approved draft, the reason is noted.

**v3 (2026-10-22):** the power strip now screws straight to the desk through its own
keyhole slots (`images/reference-strip-back-keyholes.jpg`), so this project is no longer a
strip tray. It is a **short J-hook cable channel**, 150 mm long, with a full-length edge
guide so a row of them lines up along the desk edge. Printed **on end** with **designed-in
breakaway feet**.

Earlier versions (strip tray, segmented) are superseded. The strip photos stay in
`images/` as reference for cord sizes.

---

## Concept

```
  installed, viewed from the channel's end (X into the page)

                            desk underside (Z = 0)        desk edge face (Y = 0)
  ═════════════════════════════════════════════════════════════╗┃
  ┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓  ┃┃ <- GUIDE LIP
  ┃          ROOF (flange continues over the pocket)      ┃  ┗┛
  ┗━━━┓                                         ┏━━━━━━━━━━┛
       ENTRY GAP         POCKET                 ┃▼screws▼   <- SCREW ZONE
  ┏━━━┓  (faces into       40 × 30              ┃            (heads driven from below,
  ┃   ┃   the desk)                             ┃             outside the pocket)
  ┃   ┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
  inner lip          floor              outer wall
  <- toward the desk interior                     toward the desk edge ->
```

- **Edge guide lip:** runs the full 150 mm and rises above the desk-underside plane,
  hugging the desk edge. Push it against the edge and the channel sits square and
  parallel. Mount several in a row along the edge to make a continuous run.
- **Screw zone:** a strip of flange between the guide lip and the outer wall, with two
  countersunk **#8 wood screw** holes. The heads are driven straight up from below,
  outside the pocket, so cords never block the driver.
- **Pocket:** about 40 × 30 mm inside, sized for **6–8 cords** (your strip's power cords
  plus USB leads).
- **Entry gap:** 15 mm tall, on the side facing **into the desk**, between the inner lip
  and the roof. You push cords up and in from below. Under load, cord weight pulls them
  down onto the floor, away from the gap. The inner lip's top edge is rounded so it
  doesn't scuff cords.
- **Roof:** the flange continues over the pocket, making a rigid C section rather than a
  hook hanging off a narrow flange, and it keeps cords off the desk underside.
- **Branding:** "CornerCAD" embossed on the outer wall's outer face, which faces the desk
  edge and is the side you see when you look under the desk.
- **Strip mounting** is not part of this print: the strip hangs on two screws through its
  keyholes.

## Coordinate frame

- X = channel length, centered on X = 0 (`CAD_STANDARDS.md`).
- Y = across. Desk edge face at Y = 0; the channel extends to Y < 0 under the desk, and
  the guide lip sits at Y > 0 outside the edge.
- Z = vertical. Z = 0 is the desk-underside plane (the flange top face).

---

## PARAMETERS (`Params.FCStd` → `VarSet`)

| Name | Type | Default | Group | Drives |
|---|---|---|---|---|
| `ChannelLength` | Length | 150 mm | Geometry | Pad length (and print height on end) |
| `PocketWidth` | Length | 40 mm | Geometry | Inside width, outer wall to inner lip |
| `PocketHeight` | Length | 30 mm | Geometry | Inside height, floor top to roof underside |
| `EntryGap` | Length | 15 mm | Geometry | Opening between the inner lip top and the roof |
| `WallThickness` | Length | 2.5 mm | Geometry | Outer wall and inner lip |
| `FloorThickness` | Length | 2.5 mm | Geometry | Floor (separate knob: base layers ≠ perimeters) |
| `RoofThickness` | Length | 4.0 mm | Mounting | Flange/roof thickness — screws pass through it |
| `ScrewZoneWidth` | Length | 16 mm | Mounting | Desk edge to outer-wall outer face; screws centered in it |
| `ScrewCount` | Integer | 2 | Mounting | LinearPattern occurrences |
| `ScrewEndInset` | Length | 25 mm | Mounting | First/last screw from the channel ends |
| `CountersinkDia` | Length | 9.0 mm | Mounting | #8 flat head ≈ 8.4 mm + margin |
| `CountersinkAngle` | Angle | 82° | Mounting | #8 flat head |
| `GuideLipHeight` | Length | 12 mm | EdgeGuide | Lip rise above the desk plane |
| `GuideLipThickness` | Length | 3.0 mm | EdgeGuide | Lip thickness |
| `EntryLipRadius` | Length | 1.0 mm | Finish | Round on the inner lip top and roof edge, where cords pass |
| `EdgeBreakRadius` | Length | 0.8 mm | Finish | Other exposed edges |
| `FootReach` | Length | 18 mm | PrintSupport | How far each breakaway foot reaches out from the part |
| `FootHeight` | Length | 20 mm | PrintSupport | How far up the part each foot fin rises |
| `FootThickness` | Length | 1.2 mm | PrintSupport | Fin and pad thickness (3 perimeters at 0.4) |
| `FootNeckWidth` | Length | 0.8 mm | PrintSupport | Width of each snap-off neck joining a foot to the part |
| `FootNeckHeight` | Length | 2.0 mm | PrintSupport | Neck height up the part from the bed |
| `TextString` | String | "CornerCAD" | Branding | Emboss text |
| `TextSize` | Length | 8 mm | Branding | Text size (outer wall is ~37 mm tall) |
| `TextDepth` | Length | 0.6 mm | Branding | Emboss height |
| `ScrewClearanceDia` | Length | 4.6 mm | Clearances | Screw ↔ roof: #8 shank clearance |
| `SupportGap` | Length | 0.3 mm | Clearances | Foot fin ↔ part face: air gap everywhere except the necks |

Derived in expressions (not Params):
- inner lip height = `PocketHeight − EntryGap` (15 mm)
- profile width = `ScrewZoneWidth + PocketWidth + 2·WallThickness` (61 mm) + lip
- profile depth = `RoofThickness + PocketHeight + FloorThickness` (36.5 mm)
- screw Y = `−ScrewZoneWidth / 2`; screw span = `ChannelLength − 2·ScrewEndInset` (100 mm)

Clearances stay decoupled: `ScrewClearanceDia` (screw ↔ roof) and `SupportGap`
(breakaway foot ↔ part) are separate interfaces and never share a knob.

---

## FEATURE TREE (as built)

`UnderDeskCableTray.FCStd`

**Body `Channel`: the product**
1. `ProfileSketch` on `YZ_Plane`: one closed outline (guide lip, screw zone, roof, outer
   wall, floor, inner lip), 13 H/V lines plus 7 corner arcs, fully constrained, every
   dimension bound. **Rounds are drawn in the sketch, not as Fillet features** (changed from
   the draft): radius-bound arcs have no edge-name dependency, so they can't break under the
   topological-naming problem.
   - `EdgeBreakRadius`: lip top corners, lip outer bottom, roof top inner end, bottom corners.
   - `EntryLipRadius`: roof underside edge at the entry gap.
   - **Inner lip top is a full semicircle**, radius `WallThickness / 2`, with its apex pinned at
     `RoofThickness + EntryGap` below the desk so `EntryGap` stays true. (Changed: two separate
     `EntryLipRadius` corners degenerated at `WallThickness` 2.0 in the sweep.)
2. `ChannelPad`: Pad, `SideType = Symmetric` (replaces deprecated `Midplane`),
   `Length = ChannelLength`.
3. `RoofUnderside`: DatumPlane on `XY_Plane`, offset `−RoofThickness`.
4. `ScrewSketch` on `RoofUnderside`: one circle at X = `−(ChannelLength/2 − ScrewEndInset)`,
   Y = `−ScrewZoneWidth/2`, Ø `ScrewClearanceDia`.
5. `ScrewHole`: Hole, custom 82° countersink Ø `CountersinkDia` on the underside, ThroughAll,
   `Reversed` (drills toward +Z).
6. `ScrewPattern`: LinearPattern on X, `Length = ChannelLength − 2·ScrewEndInset`,
   `Occurrences = ScrewCount`. Body Tip is set to the pattern explicitly before the logo
   (otherwise the logo built on the single hole and filled the second one back in).
7. `LogoText`: Draft ShapeString (inside the body), Middle-Center justified, its Placement
   bound to Y = `−ScrewZoneWidth`, Z = mid outer wall, reading correctly from the desk-edge
   side. (Changed: positioned by bound Placement instead of an `OuterFaceDatum`.)
8. `LogoEmboss`: Pad of `LogoText`, `TextDepth`, toward +Y.

**`PrintFeet`: designed-in breakaway supports (print pose only)**, built with Part, not PartDesign
(changed: a PartDesign body can't hold four disjoint solids).
9. Four `Foot<Name>Fin` sketches (Lip, Inner, Bottom, Top), each placed by a bound Placement in
   an XY or XZ plane through the profile. Each holds a right-triangle fin: `FootReach` along the
   bed, `FootHeight` up the part, starting `SupportGap` off the part face.
10. Four `Foot<Name>Neck` sketches: `FootNeckHeight` tall bridges that overlap `SupportGap` into
    the part and `FootThickness` into the fin, so each foot fuses as one solid.
11. `Part::Extrusion` of each, symmetric: `FootThickness` (fins) and `FootNeckWidth` (necks).
12. `PrintFeet`: `Part::MultiFuse` of all eight → 4 solids.
    Fin and neck dimensions are **signed point-to-point** `DistanceX`/`DistanceY`. (Changed: an
    unsigned `Distance` let the Inner neck flip sides at `PocketWidth` 60.)

Export (next step, after Bradley's review): `3D_printer_3mf_workflow` (2cv001) exports one
object per run, named by its Label, next to the FCStd. Print-pose rotation and the
Channel + PrintFeet combination will be set up for it then.

No sketch attaches to a feature face.

## CONSTRAINT STRATEGY

- Every sketch dimension bound as `<<Params>>#VarSet.Name`; no bare literals.
- `ProfileSketch` anchored to the sketch origin: the lip's inner face is coincident with
  the V axis (Y = 0) and the roof top with the H axis (Z = 0). Everything is dimensioned
  outward from those two lines, so DoF = 0 without external geometry.
- `FeetSketch` re-derives the profile's corner positions from the same Params rather than
  projecting `Channel` edges, so the feet follow any profile change and never cross-link
  bodies (the MagicCardBox `Sketch002` silent-stale failure).

## VALIDATION

**INVALID: these results came from a headless FreeCAD 1.1.0 in a sandbox, not Bradley's laptop, and do not count. Re-run on the laptop with the `test_env_guard` banner.** The sandbox run reported the following, kept only as a lead: Audit clean. `Channel` is a valid single solid,
bbox X ±75, Y −61…+3, Z −36.5…+12, volume 77.1 cm³ (≈ 98 g PETG). Both screw bores are open,
countersink geometry is correct, inner-lip apex sits at −19.0 as specified, and all four fin gaps
measure 0.30 mm. In the print pose there are zero downward faces steeper than 45° (only the
0.6 mm logo letter undersides, which print fine). Sweep (ChannelLength 100/250, PocketWidth
30/60, PocketHeight 20/40, EntryGap 10/20, Wall/Floor 2.0, combined 60/40/250, and the 40 mm
coupon): every case rebuilds valid as a single solid with 4 feet and restores exactly. Coupon
note: set `TextSize` 4 for the 40 mm coupon, or the 59 mm logo overhangs the ends.

Planned checks:

1. Recompute clean; `Channel` and `PrintFeet` each valid, single solid, closed shell.
2. `python3 scripts/audit_parametric.py` → zero issues.
3. In the print pose (rotated so X is up): `Channel` has no downward-facing surface
   steeper than 45° except the bed face. Check by face-normal scan.
4. Measure `distToShape(Channel, PrintFeet)`: it should equal `SupportGap` everywhere
   except at the necks, so the feet touch only through the necks.
5. Parameter sweep: `ChannelLength` 100/150/250, `PocketWidth` 30/60, `PocketHeight` 20/40,
   `EntryGap` 10/20. Rebuild, re-check validity, and confirm the feet track the profile.
6. Isometric + end-view renders to `images/`.
7. **Test coupon first:** `ChannelLength = 40`, `ScrewCount = 1`. Check that the feet snap
   off cleanly, the screw seats flush, the lip fits your desk edge, and 6–8 of your cords
   push in through the gap and stay put.

## PRINT POSE

On end: the channel stands 150 mm tall with X vertical. Every layer is the full profile, so:
- there are no overhangs anywhere on the part, and the countersink cone prints as a
  sideways hole (acceptable at 82° on a 9 mm head);
- the corners between roof, walls and floor are built within each layer, their strongest
  direction under cord weight;
- the snap-off feet widen the 61 × 37 mm footprint to about 97 × 73 mm, so a 150 mm tower
  doesn't wobble or tip at speed. No slicer supports or brim needed.

---

## Assumptions (change in Params if wrong)

- **Desk at least 15 mm thick** under the 12 mm guide lip (`GuideLipHeight`).
- **#8 × 5/8″ (16 mm) flat-head wood screws.** The 4 mm roof plus 12 mm of bite suits
  most desktops; use 1/2″ screws on a thin top.
