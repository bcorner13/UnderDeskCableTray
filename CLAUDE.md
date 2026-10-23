# Project rules — UnderDeskCableTray

The channel is one cross-section sketch padded along X, and the breakaway print feet are sized from the same Params. So `ProfileSketch` is the single point of failure: if one of its dimensions is a literal, or the feet sketch projects `Channel` edges instead of re-deriving them, a `PocketWidth` change will break the edge guide or leave the feet fused to (or floating off) the part.

---

## Hard rules (this project)

These restate the global rules in `~/.claude/CLAUDE.md` with project-specific context. Cite the actual incident or constraint that motivates each one — generic restatements are useless because the global file already has them.

1. **Everything parametric.** Highest-risk objects are `ProfileSketch` (one closed outline: guide lip, screw zone, roof, outer wall, floor, inner lip), the screw `LinearPattern` (length must be the expression `ChannelLength − 2·ScrewEndInset`, `Occurrences` bound to `ScrewCount`), and `FeetSketch`, which must re-derive the profile corners from Params, never from projected `Channel` geometry. No prior coordinate-edit incident — the project is new.

2. **No fixing geometry by editing raw sketch coordinates.** No prior incident in this project; rule applies preventively.

3. **Attach sketches to datum planes, not feature faces.** No prior DAG incident; rule applies preventively. The screw sketch attaches to the `RoofUnderside` datum (offset from `XY_Plane`), never to the pad face; the logo attaches to `OuterFaceDatum`.

4. **Clearance concepts stay decoupled.**
   - `ScrewClearanceDia` — #8 wood screw shank ↔ roof hole
   - `SupportGap` — breakaway print feet ↔ part face (air gap except at the necks)
   Add more as needed; never reuse one clearance Param across distinct physical interfaces.

---

## Assembly architecture

Single printed part per channel; mount as many as needed in a row along the desk edge. The power strip is **not** held by this part. It screws to the desk through its own keyhole slots (`images/reference-strip-back-keyholes.jpg`). How the channel mates to the world:

- **Desk underside**: the roof top face (Z = 0) sits flush against it. Two #8 wood screws go up through the **screw zone** (between the guide lip and the outer wall), heads in 82° countersinks on the underside, outside the cord pocket.
- **Desk edge**: the guide lip (inner face at Y = 0) rises `GuideLipHeight` above Z = 0 and butts the desk edge face, setting the setback and keeping the channel parallel to the edge.
- **Cords**: enter through the `EntryGap` on the side facing **into the desk** (between the inner lip top and the roof), pushed up and in from below. They rest on the floor, away from the gap. The inner lip top is rounded (`EntryLipRadius`).
- **Logo**: "CornerCAD" (`TextString`) embossed on the outer wall's outer face, which faces the desk edge.
- **Print feet** (`PrintFeet` body): exist only for printing on end. Tapered fins at the bottom end, `SupportGap` off the part, joined by `FootNeckWidth` necks on the bottom 2 mm; they snap off by hand.

Reference photos (strip and cords, from the superseded strip-tray design): `images/reference-*.jpg`.

---

## Files in this project

| File | Role | Depends on | Status |
|---|---|---|---|
| `Params.FCStd` | VarSet — 26 parametric variables | — | ✅ created by `macros/00-create_params.FCMacro` |
| `UnderDeskCableTray.FCStd` | Body `Channel` (product) + `PrintFeet` (Part MultiFuse, breakaway supports) | `Params.FCStd` | ⚠️ built 2026-10-23 by `macros/01-build_channel.FCMacro` in a sandbox, **unverified** |

**Not verified.** The file was built and "tested" in a headless FreeCAD 1.1.0 that a session installed in its own sandbox, against the stop rule. Those audit/sweep results are **invalid**: they did not run on Bradley's laptop. Re-run every check on the laptop (FreeCAD GUI via the MCP bridge, `test_env_guard` banner quoted) before treating anything here as passing.

Build gotchas found by the sweep (all fixed in macro 01; don't reintroduce):
- **Body Tip after a LinearPattern**: set `body.Tip = pattern` before adding the next feature, or it builds on the single Hole and silently fills the patterned holes.
- **Feet sketches use signed `DistanceX`/`DistanceY` between points**, never unsigned `Distance`; the unsigned version let a neck flip sides at `PocketWidth` 60.
- **Necks overlap** into both part and fin; face-to-face contact fused unreliably.
- **Inner lip top is a full round** (radius `WallThickness/2`, apex pinned). Two corner radii degenerated at `WallThickness` 2.0 and the sketch did not recover on restore.
- **Cross-doc expressions need a saved owner doc**: macro 01 saves the new document once, empty, before building. That's the only automatic save.

---

## Params variables (summary)

`Params.FCStd` has 25 variables (defined in `macros/00-create_params.FCMacro`, documented in `plan.md`):
Geometry (`ChannelLength`, `PocketWidth`, `PocketHeight`, `EntryGap`, `WallThickness`, `FloorThickness`),
Mounting (`RoofThickness`, `ScrewZoneWidth`, `ScrewCount`, `ScrewEndInset`, `CountersinkDia`, `CountersinkAngle`),
EdgeGuide (`GuideLipHeight`, `GuideLipThickness`),
Finish (`EntryLipRadius`, `EdgeBreakRadius`),
PrintSupport (`FootReach`, `FootHeight`, `FootThickness`, `FootNeckWidth`),
Branding (`TextString`, `TextSize`, `TextDepth`),
Clearances (`ScrewClearanceDia`, `SupportGap`).

---

## How to verify your change didn't break parametric

After any FreeCAD edit, before considering the task done:

```bash
python3 scripts/audit_parametric.py
```

This script flags:
- Sketches with 0 constraints
- Sketches with dimensional constraints lacking expression bindings
- Sketches attached to feature faces (DAG risk)
- Params variables used nowhere (dead Params)

The script is authoritative. If it reports violations, fix them via a `macros/*.FCMacro` change before saving or committing — never by direct coordinate edits or FCStd XML surgery.

No exemptions; audit must be clean.

---

## Memory files (deeper context)

`~/.claude/projects/[encoded-path]/memory/MEMORY.md` indexes the persistent memories for this project. If you're unsure *why* a rule exists, read those files first — they trace each rule to a concrete past incident.

No project-scoped memories yet — this is a fresh project.

---

## Workflow notes

**Invariant (apply to every FreeCAD project — do not edit):**

- **Inspect/edit FreeCAD models via the MCP bridge — never with shell tools.** Do **not** `unzip`/`grep`/`cat`/`sed`/`strings`/etc. a `.FCStd`. Use the FreeCAD Robust MCP server: `get_connection_status` first, then `open_document`, `list_objects`, `inspect_object`, `execute_python`, and macros. This is **enforced** by a PreToolUse hook in `.claude/settings.json` (copied from the root `settings.template.json` during bootstrap) — raw shell access to `.FCStd` is blocked. Only fall back to read-only `unzip` if the MCP bridge is genuinely unreachable, and ask the user first.
- **MCP server auto-starts with FreeCAD.** If an `mcp__freecad__*` call fails, the right interpretation is "FreeCAD isn't running" — ask whether to launch it. Do **not** silently fall back to `unzip` + XML parsing, and do **not** retry the same MCP call.
- **Write changes as `macros/*.FCMacro` files**, not direct XML edits. Reasons: reviewable, re-runnable, idempotent-friendly, uses FreeCAD's own serialization.
- **Cross-document expressions**: use the canonical form `<<Params>>#VarSet.VarName`. The shorter `<<Params>>.VarName` form sometimes fails with "Params not found."
- **Run `python3 scripts/audit_parametric.py` before committing.** If it reports violations, fix via macro, not by editing FCStd XML.
- **Stop means stop; only Bradley's laptop counts.** If the laptop's FreeCAD or MCP bridge is unreachable, the plan isn't approved, or required files are missing, the task is **blocked**: report it with the evidence and end the turn. Do not install, download, or run any other FreeCAD, MCP server, VM, sandbox, or headless instance, or take any other route to the result not described here, including routes this file doesn't mention. Every validation run (FreeCAD checks, sweeps, audit) calls `require_laptop()` from `scripts/test_env_guard.py` first and quotes its banner. A result without a `TEST ENVIRONMENT VERIFIED` banner is **invalid**, not partial evidence, and must never be reported as passing. Never edit, stub, or bypass the guard, and never run `--enroll`; only Bradley enrolls the machine.

**Project-specific:**

- Design history: v1/v2 were a power-strip tray (strip on its side, segmented). Superseded 2026-10-22 when Bradley chose to screw the strip to the desk through its keyholes; the project is now a 150 mm cable channel. Don't resurrect strip-cradle geometry.
- Print pose is **on end** (X vertical, 150 mm tall) with the designed-in `PrintFeet`. Exports must rotate into that pose. Ship both a with-feet STL and a feet-free STL.
- `scripts/audit_parametric.py` was copied from `Lockbox Bracket/` at bootstrap.
- Print a 40 mm test coupon (`ChannelLength = 40`, `ScrewCount = 1`) before full channels. Check that the feet snap off, the screw seats, the lip fits the desk, and 6–8 cords stay put.
- Logo font: `/Users/bradleycorner/Library/Fonts/DejaVuSans.ttf` (macro 01 falls back to other DejaVuSans locations if missing).
- Export uses `3D_printer_3mf_workflow` (2cv001, successor to the old `3d_printer_workflow`): one object per run, file named by Label, written next to the FCStd. A post-processing command can copy it into `3mf/`.
- Coupon: also set `TextSize` 4, or the logo overhangs a 40 mm channel.

---

## Print profile

**No successful test print yet — profile TBD.**
