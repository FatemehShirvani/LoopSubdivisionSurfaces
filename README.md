# Loop Subdivision Surfaces

An interactive study of Loop subdivision for triangular meshes, developed in C++ and OpenGL as part of **Fundamentals of Computer Graphics (IGR202)** at Institut Polytechnique de Paris.

The implementation refines each input triangle into four faces while repositioning the mesh vertices toward a smooth limit surface. It supports interactive subdivision, wireframe inspection, and undo for comparing refinement levels.

[View the portfolio teaser](media/subdivision-loop.mp4)

## Method

For every refinement step, the implementation:

1. Builds one-ring vertex neighborhoods and maps each undirected edge to its adjacent triangles.
2. Detects boundary edges and boundary vertices from edge incidence.
3. Updates original, or even, vertices using Loop's valence-dependent mask; boundary vertices use the `1/8, 3/4, 1/8` rule.
4. Creates one shared odd vertex per edge. Interior edges use the `3/8, 3/8, 1/8, 1/8` mask, while boundary edges use their midpoint.
5. Replaces every original triangle with four consistently indexed triangles and recomputes rendering attributes.

The viewer also keeps a bounded history for undo and rejects refinement beyond a triangle-count threshold to avoid accidental exponential growth.

See [report.pdf](docs/report.pdf) for the full write-up, equations, implementation details, and references.

## Source availability

The implementation was built on teaching framework code whose header explicitly restricts redistribution. The repository therefore contains the technical report and visual result, but not the course framework or source code.

## Reference

Charles Loop, *Smooth Subdivision Surfaces Based on Triangles*, Master's thesis, University of Utah, 1987.
