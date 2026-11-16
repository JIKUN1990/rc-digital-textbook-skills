---
name: rc-material-interactive
description: Create or extend interactive material teaching resources that link SVG stress–strain curves to synchronized Three.js specimen states using the project's TypeScript Material Interactive Engine. Use for steel, concrete, FRP, tensile/compression, curve data, milestones, and schematic failure animations.
---

# RC Material Interactive

Use the shared TypeScript engine at `shared/material-interactive/` for 《混凝土结构设计原理》 material interactions. Do not replace or change the distinct responsibilities of `.agents/skills/rc-math-svg/`.

## Workflow

1. Read root `AGENTS.md`, `README.md`, and the target resource's `SOURCE.md`, README, and relevant code. Check existing files and tests before editing. Preserve completed resources; create a named backup before a broad migration.
2. Extract only source-supported formulas, samples, units, sign conventions, and milestone meanings. Record source, model type, applicable strain range, and uncertainty. Mark image-estimated curves as schematic; never promote them to measured data or verified material parameters.
3. Implement a `MaterialModel` that owns stress, samples, milestones, response stages, metadata, and parameter validation. Use dimensionless strain internally and MPa for stress. For compression, use a nonnegative strain magnitude and state that convention in metadata. Keep stress–strain arrays monotonic in strain; never invert stress on a descending branch.
4. Implement or select a `GeometryAdapter`/`DeformationAdapter` for that material's specimen and failure mode. Keep constitutive functions out of Three.js geometry updates. Use visual magnification only through a named configuration value and label schematic fields clearly.
5. Configure the resource with `MaterialInteractiveConfig` and call `createMaterialInteractive`. Keep teaching copy, axis labels, milestone labels, and specimen-specific options in the resource configuration.
6. Drive curve position, readouts, phase labels, and specimen geometry from the same `AnimationController` snapshot via `StateSynchronizer`. Test pause/resume, speed, slider scrubbing while playing, seeking by strain and milestone, reset, and replay.
7. Keep image-based model reconstruction separate from numeric visualization. Use `img2threejs` only when an object in a supplied reference image needs procedural 3D reconstruction; ordinary chart, mesh, and vertex-color work should use the existing code and local Three.js.
8. Compile and stage local runtime dependencies with `python tools/build_material_interactive.py`. This copies compiled engine modules into the target resource so a later HEP5 payload can remain self-contained. Do not create or publish a HEP5 unless requested.
9. Run the material-model and controller tests, then the target resource's Edge/WebGL browser smoke test. Inspect initial, milestone, post-peak/localization, just-before-failure, and final states; check console/network errors, responsive layout, and adapter disposal.
10. Write or update the audit report with source limits, component API, completed tests, extension-only fixtures, local preview links, and what still needs real material data or platform validation.

## Shared architecture

- `core/` owns material and snapshot types, registry, animation, and state fan-out.
- `curve/` owns generic SVG axes/series, marker selection, pointer inspection, and SVG serialization.
- `three/` owns the scene/camera/OrbitControls lifecycle and adapter contracts.
- `controls/` owns play/pause, range slider, and reset bindings.
- `materials/` contains material math and clearly marked test fixtures.
- `specimens/` contains reusable geometry/deformation adapters.

Do not hardcode material-specific milestone IDs or stage names in shared rendering and control code. Put those values in the material model or resource configuration. Keep test-only hard-steel, concrete, and other example curves visibly marked as synthetic; do not infer official parameters without an authoritative source.

## Project commands

Run from the project root:

```powershell
python tools\build_material_interactive.py
node tests\test_intro_005_curve.mjs
node tests\test_material_interactive_core.mjs
node tests\browser_smoke_intro_005.mjs
node tests\browser_smoke_material_examples.mjs
```

Local preview: `http://127.0.0.1:8765/modules/<module_id>/`. Component examples: `http://127.0.0.1:8765/examples/material-interactive/`.
