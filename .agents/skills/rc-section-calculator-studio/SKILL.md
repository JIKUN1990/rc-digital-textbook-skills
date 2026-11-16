---
name: rc-section-calculator-studio
description: Build or extend interactive reinforced-concrete design calculators focused on required/provided reinforcement, practical bar selection, and cover/spacing/layer checks, with synchronized SVG and Three.js views. Use for flexure and new section/action families; derive each family from its own source instead of reusing rectangular-flexure equations.
---

# RC Section Calculator Studio

Use this skill when the task centers on a local interactive RC design calculation and reinforcement detailing. For a task centered on animating one supplied figure and its derivation, the project’s `rc-section-3d-derivation` skill may be a better fit. Keep each deliverable within the project structure and conventions in `AGENTS.md`. Reuse the visual and implementation lessons from `flexure_001_single_rectangular_v2` and `flexure_002_double_rectangular_v1`, while treating their equations as specific to those section types.

## Establish the engineering basis first

- Inspect the supplied textbook figures, derivation notes, examples, current module, and `AGENTS.md` before editing. Treat source files as read-only. Record the source title, edition, page/figure, and reuse permission in the module provenance note; mark missing details “待核实”.
- Make a short symbol and assumptions map: sign convention, units, material grades, geometry, load/effect, equation source, applicability limits, and which displayed quantities are calculated versus user inputs.
- Keep textbook examples, code-based design values, and teaching simplifications distinguishable. Cite the exact applicable standard, edition, clause/table, and any textbook equation numbers next to normative inputs or in a compact source note. Do not conflate GB/T 50010 concrete/reinforcement design provisions with seismic standards such as GB 50011. If the user names a code edition, preserve it and verify each value against the cited source; never silently update it or invent missing values.
- Make code-derived material values respond to the selected concrete and reinforcing-steel grades. Keep genuinely editable design assumptions editable; make derived fields read-only. Do not add unused material inputs merely for symmetry.

## Layout and shared interaction model

For the established flexural calculator layout, use a responsive visual row followed by a calculation workbench:

1. Visual row: beam 3D cutaway at left; one panel at right with the section geometry/compression block above the internal-force and lever-arm diagram.
2. Workbench: input parameters, stepwise formula derivation, and live results/checks in three coordinated columns on wide screens. Stack these panels at tablet and phone widths without clipping controls or formulas.
3. Keep labels and equation text only where they help interpret a view. Remove redundant explanatory captions when the user requests a cleaner page. Use readable formula and result sizes, visible units, semantic controls, and concise Chinese status text.

Keep one controller state as the source for the solver, input summary, derivation substitutions, result checks, section SVG, force SVG, and Three.js scene. A parameter or bar-choice change must update all dependent views in the same interaction. Separate responsibilities into a pure calculation layer, state/controller, SVG renderers, and 3D renderer; do not put design equations inside drawing code. Render only trusted static formula templates with local math assets, never execute user-entered formula text.

## Formula and standards discipline

- Implement the equation path and branches from the supplied derivation, including applicable limits and invalid states. Keep capacity, equilibrium, detailing, and applicability checks separate. Show enough substitutions and intermediate values for a student to follow the selected branch.
- Name equivalent rectangular compression-block depth `x` accurately; do not call it the strain neutral-axis depth unless the source explicitly defines it that way.
- Keep unit conversion explicit (for example, kN·m to N·mm) and use consistent internal units. Make boundary comparisons numerically stable and report a useful reason when an input lies outside the solution range.
- Do not transfer a formula just because symbols look similar. T-sections need flange/web piecewise compression-block logic and the correct effective flange basis. Shear sections need their own diagonal-compression/truss or code model and stirrup checks. Axial tension/compression needs its own force/strain assumptions, eccentricity conditions, and limits. Start each family from its cited source, then define its solver and diagram independently.
- Keep the calculator's scope visible: a teaching calculator or selected checks must not imply a complete engineering design review.

## Reinforcement detailing and lever arms

Treat reinforcement area selection, physical arrangement, and lever-arm assumptions as related but separate concerns.

- Determine the outermost longitudinal-bar cover from the applicable exposure class, design working life, concrete grade, and stirrup detail. For beams, distinguish cover `c` to the outer stirrup from `c_s` to a longitudinal bar; the usual geometric relationship is `c_s = c + d_sv`, subject to the source's minimum bar-cover rule. Keep this cover check separate from the approximate lever-arm rule.
- Use the project’s stated beam teaching approximation when applicable: one layer, `a_s = c_s + 10 mm`; two layers, `a_s = c_s + 35 mm`. Apply the same rule to `a_s′` for compression reinforcement. Select the one/two-layer case from the adopted detailing; do not replace these expressions with a diameter-based centroid formula. In particular, do not add `d/2` to this approximate `a_s`. Derive `h_0 = h - a_s` from that value. If another supplied source or member family defines a different rule, use that source for that family instead.
- For horizontal bar centers, use actual dimensions rather than arbitrary width percentages. For a row with bar diameter `d_i`, its edge-center clearance must include cover, stirrup diameter, and bar radius, with the applicable minimum cover also enforced. For equal-diameter bars that fit symmetrically, distribute centers uniformly between the allowed edge positions. For mixed diameters, compute each adjacent center spacing from required clear spacing plus the two radii, then distribute any remaining width symmetrically. Keep center-to-center spacing distinct from clear spacing.
- Check side cover, stirrup interference, same-row clear spacing, inter-layer clear spacing, bar count, row count, and vertical fit. Use the applicable source-specific limits (the recent beam resource uses lower clear spacing `max(d, 25 mm)`, upper clear spacing `max(1.5d, 30 mm)`, and inter-layer clear spacing `max(d, 25 mm)`). Do not silently move user dimensions or relax a failed condition.
- If selecting practical bar combinations, display required area, provided area, and percent difference. In this project’s flexural calculator, only offer combinations within ±5% of required area and passing detailing checks; if none qualify, say so and let the user revise the section or target. Do not show a failed construction option as selectable. For known `A_s′`, derive its area from the chosen count and diameter controls rather than offering a second unrelated bar-pick list.
- Support explicit row definitions for mixed bar groups when the source/user specifies them (for example, three smaller bars in the first row and larger corner bars plus a center bar in the next row). Do not generalize one example’s pattern as a universal reinforcement rule.
- Keep calculated lever arms and drawn bar geometry consistent with the selected layout, but preserve the source’s approximate `a_s/a_s′` rather than silently recalculating it from selected diameters. If a visual row spacing is not specified, state or mark the schematic assumption and check that the depiction remains physically possible.

## SVG and Three.js synchronization

- Drive section SVG and Three.js longitudinal bars from the same row-and-bar layout object. The section SVG must show the actual selected bar count, diameter/group, symmetric horizontal centers, layers, cover/stirrup boundary where useful, and compression block depth. Avoid reducing a selected multi-row layout to generic dots.
- Place compression and tension groups at their respective faces using `a_s′` and `a_s` conventions. Use a consistent axis/sign convention across the section, force diagram, calculations, and 3D scene.
- Make longitudinal bars continuous along the beam axis in Three.js; place the cut face/circular bar ends from the same cross-section coordinates used by SVG. Concrete cutaway, compression-zone overlay, steel visibility, transparency, camera reset, and resize behavior should not alter the calculation state.
- Keep force arrows, labels, dimension lines, and values from overlapping. Move labels away from arrowheads and dimension marks; show computed `x`, `z`, force, and moment at their relevant diagram locations rather than duplicating tiny captions below the diagram.
- Clearly identify the 3D view as a mechanics/detailing illustration, not finite-element analysis. Provide a graceful fallback when WebGL is unavailable and keep all assets local for offline playback.

## Adaptation by member family

Before extending the calculator, add a family-specific model note rather than stretching the rectangular-flexure model:

| Family | Reuse | Re-derive before implementation |
| --- | --- | --- |
| Single/double rectangular flexure | Three-panel workbench, materials, detailing state, synchronized SVG/3D | Equilibrium branches, `x` limits, `A_s`/`A_s′`, boundary checks from the cited flexure source |
| T-section flexure | Workbench and shared render/controller patterns | Flange effective width, compression block crossing flange/web, section centroid and piecewise capacity/equilibrium |
| Shear/inclined section | Inputs, result cards, SVG/3D scaffolding | Shear mechanism, diagonal strut/tie or code equations, stirrup contribution, spacing and anchorage limits |
| Axial tension/compression | Material/detailing controls and visualization patterns | Force convention, strain distribution, eccentricity, concrete contribution/neglect, reinforcement yield and code limits |

Share only generic geometry, formatting, bar-layout, and rendering helpers. Keep solvers and family-specific checks separate so a flexure branch cannot be selected accidentally for shear or axial loading.

## Implementation and delivery

- Prefer plain HTML, CSS, JavaScript, and SVG with local assets, relative paths, and no CDN/API requirement, following project conventions. Keep each interactive resource self-contained under `modules/<resource-id>/` and preserve completed modules unless asked to change them.
- Implement the pure solver before wiring the UI. Use at least one sourced hand calculation for the default/example case; inspect all governing branches, invalid inputs, material changes, one/two-layer transitions, no-valid-bar-choice states, and responsive layouts when verification is part of the task. Report only checks actually performed.
- Keep static web generation and HEP5 packaging as separate deliverables. Build/export `.hep5` only when explicitly requested; local playback is not evidence of 云创 acceptance.
- End with concise implementation notes: equations/standard basis, detailing assumptions, what was verified, and unresolved items marked “待核实”.
