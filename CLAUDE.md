# Superformula explorer — guide for Claude

An interactive visualizer for Gielis' superformula: two parameter sets combine
into a 3D supershape (spherical product), with presets for the shape
categories in `shape-categories.md`, a randomize button, and a 2D preview of
Set 1.

It doubles as the tuning tool for nijimu (`../nijimu`), whose memory
artifacts are generated from the same formula and categories in
`nijimu/src/app/lib/superformula.ts`. Keep the two consistent when the formula
or the category ranges change.

## Layout

- `index.html`: the whole superformula app, with its markup, CSS and script in one file.
- `superquadric.html`: the Superquadric CSG explorer, kept as its own file.
- `shape-categories.md`: categories, parameter ranges and sample configs.
  This is the reference the presets come from.
- `README.md`: the public description.

## Running it

There's no build step and no package manager. Open `index.html` directly, or
serve the folder (`python3 -m http.server 5190`). The `static` entry in
`.claude/launch.json` does this for the preview pane.

## How it works

- `P.A` (Set 1) and `P.B` (Set 2) hold `{m, n1, n2, n3, a, b}`. Every slider,
  preset or randomize action edits `P` and calls `sync()`, which updates the
  controls and redraws the 2D curve (`draw2`) and the mesh (`build`).
- `sf(angle, set)` is the formula. It clamps r at 50, per the practical
  notes in the reference.
- `build()` samples θ ∈ [−π, π] (`NU` = 260) by φ ∈ [−π/2, π/2]
  (`NV` = 130) and normalises the shape to fit. The formula is z-up, so
  positions are written as (x, z, y) to stand the profile along three.js's
  y axis. Geometry is replaced in place; the material stays on the mesh.
- Materials are Clay (matte grey Phong), Glass, Metal (high-contrast
  zebra reflection lines, a dark mirror), and Negative (a white ground
  with accumulated dark glass, like a film negative). Glass, the reflection
  layer and the silhouette edge share that geometry.
- three.js is **r128**, loaded as the global `THREE` build from cdnjs.
  Newer releases dropped that build. Moving to a newer version means
  switching to ES modules and an import map, so do it on purpose, not as a
  side effect.

## Conventions

- Keep it a single dependency-free HTML file unless there's a real reason not to.
- Colours are CSS variables in `:root`, and controls are built in JS from `specs`.
- Respect `prefers-reduced-motion`, which already disables the auto-rotate.

## Known rough edges

- The seam and pole rows are duplicated, not welded, so `computeVertexNormals`
  can leave a shading seam on closed shapes.
- `sf` returns 0 when the inner sum is 0, where clamping to 50 would match
  the reference's advice.
