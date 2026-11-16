---
name: rc-section-3d-derivation
description: Build local interactive reinforced-concrete section teaching resources from a textbook section image and formula notes, synchronizing Three.js geometry, SVG section and force diagrams, calculations, and stepwise derivation. Use for RC section mechanics such as flexure or axial-load sections; not for constitutive material curves or full structural analysis.
---

# RC Section 3D Derivation

Use this skill for a self-contained offline teaching module that connects a textbook RC section, its force diagram, and its derivation. Keep the source image and formula notes read-only. Start with the repository AGENTS.md and identify the exact image and formula source; do not substitute a nearby or previously used figure. Record edition, page/figure, source, and permission status in the module provenance note, leaving unknown facts as “待核实”.

## Separation of responsibilities

- **Material inputs:** keep concrete/steel strengths and coefficients explicit and sourced. Distinguish a teaching example from code-based design values.
- **Section geometry:** derive dependent dimensions such as h0 = h - as; do not store mutually inconsistent independent values.
- **Calculation:** implement equations and input checks as pure functions, using consistent units. Keep equilibrium, capacity, and applicability checks distinct. If a user-supplied limit governs a result, recompute dependent forces, geometry, and moment.
- **Visualization:** render controllable 2D geometry as SVG and use Three.js only for the requested spatial specimen/cutaway. Label reinforcement depictions as schematic where area is not encoded by bar count. Do not imply stress analysis or finite-element behavior.
- **Teaching:** keep source equations, symbols, units, assumptions, numeric substitution, and unresolved details visible. Tie each derivation step to the relevant visual region and preserve textbook equation numbering.

## Implementation workflow

1. Read the supplied source and existing project components. Identify symbol definitions, sign convention, formulas, units, parameters, assumptions, and uncertain/illegible details before coding.
2. Write and test the pure solver first, including boundaries, invalid inputs, conversions, residuals, and design applicability. Use independent expected values. Never provide missing normative limits by guesswork.
3. Add the parameterized SVG section and internal-force diagram. Check force direction, lever-arm endpoints, application points, labels, units, and tooltip/accessibility behavior against the source.
4. Add the cutaway Three.js beam with responsive resizing, camera controls, visibility switches, a reset view, and a non-WebGL fallback. Keep material geometry and calculation in separate modules.
5. Connect inputs, solver, SVG, Three.js and derivation through one controller state. Ensure each interaction updates all dependent views together.
6. Render math from trusted static formula strings with local KaTeX; never execute formula text. Preserve source equation numbers and explain values in ordinary text.
7. Run the requested unit and local-browser checks. Inspect default, changed-parameter, design, formula-step, and narrow-screen states; capture screenshots when browser automation is available.
8. Record what was tested, what remains uncertain, and whether any cloud platform was actually tested. Local playback is not cloud compatibility evidence.

## Project conventions

- Keep the resource in its own modules/<resource-id>/ folder and reusable section components under shared/rc-section-interactive/.
- Prefer the project's local Three.js, KaTeX and SVG support; keep paths relative and avoid CDN/API dependencies.
- Do not alter completed modules or supplied source files while adding a section resource.
- Create a HEP5 package only when the task asks for one; keep cloud upload as a separate action.
- Leave extension points for other section families without implementing them unless requested.
