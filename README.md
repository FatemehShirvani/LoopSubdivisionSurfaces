# Loop Subdivision Surfaces

An interactive study of Loop subdivision for triangular meshes, developed in C++ and OpenGL as part of **Fundamentals of Computer Graphics (IGR202)** at Institut Polytechnique de Paris.

The implementation refines each input triangle into four faces while repositioning vertices toward a smooth limit surface. It supports interactive subdivision, wireframe inspection, bounded refinement, and undo for comparing levels.

[View the portfolio teaser](media/subdivision-loop.mp4)

## Why Subdivision Matters

Simply splitting every triangle at its edge midpoints increases resolution but preserves the original piecewise-linear shape. Loop subdivision combines refinement with weighted vertex repositioning, producing an approximating surface that becomes progressively smoother while retaining triangular connectivity.

## Algorithm

For every refinement step, the implementation:

1. Builds one-ring vertex neighborhoods and maps each undirected edge to its adjacent triangles.
2. Detects boundary edges and vertices from edge incidence.
3. Repositions the original, or even, vertices.
4. Creates exactly one shared odd vertex per original edge.
5. Replaces every original triangle with four consistently oriented triangles.
6. Recomputes normals and texture coordinates for rendering.

### Even Vertices

For an interior vertex `v` with valence `n`, the updated position is

```text
v' = (1 - n * alpha_n) * v + alpha_n * sum(neighbors)

alpha_n = (1/n) * (5/8 - (3/8 + cos(2*pi/n)/4)^2)
```

This valence-dependent mask handles both regular and extraordinary vertices. Boundary vertices use the cubic B-spline boundary rule:

```text
v' = 3/4 * v + 1/8 * (boundary_neighbor_1 + boundary_neighbor_2)
```

### Odd Vertices

For an interior edge with endpoints `v1`, `v2` and opposite vertices `v3`, `v4`, the new edge vertex is

```text
v_odd = 3/8 * (v1 + v2) + 1/8 * (v3 + v4)
```

An edge with only one adjacent triangle is a boundary edge and uses its midpoint:

```text
v_odd = 1/2 * (v1 + v2)
```

## Data Structures

- `Edge` stores sorted endpoint indices, giving every undirected edge a stable key.
- `trianglesOnEdge` records edge incidence for boundary detection and opposite-vertex lookup.
- `neighboringVertices` stores each original vertex's one-ring neighborhood and valence.
- `newVertexOnEdge` prevents adjacent triangles from creating duplicate odd vertices.

Separating the old and new position arrays is essential: every mask reads only level `k` positions while constructing level `k+1`.

## Complexity and Safeguards

Each pass is linear in the number of vertices, edges, and triangles, with ordered maps adding lookup overhead. Every step multiplies the face count by four, so the viewer rejects refinement beyond 200,000 triangles. A history of up to ten mesh states supports interactive undo.

## Results

Tests on a triangulated sphere and the Suzanne mesh show the expected distinction between linear refinement and Loop subdivision. Linear refinement only increases tessellation, whereas the Loop masks round coarse silhouettes and converge toward a smooth limit surface. Wireframe mode exposes the four-to-one face refinement and confirms that neighboring faces share their edge vertices.

## Limitations

- The implementation targets manifold triangular meshes.
- Sharp creases and user-defined corner tags are not supported.
- Ordered maps favor clarity over the performance of a dedicated half-edge structure.
- The viewer is intended for interactive study rather than production-scale assets.

## Source Availability

The implementation was built on teaching framework code whose header explicitly restricts redistribution. This repository therefore documents the method and visual result without redistributing the framework or source files.

## References

1. Charles Loop. [Smooth Subdivision Surfaces Based on Triangles](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/thesis-10.pdf). Master's thesis, University of Utah, 1987.
2. Joe Warren and Henrik Weimer. *Subdivision Methods for Geometric Design*. Morgan Kaufmann, 2001.
3. Denis Zorin and Peter Schroder. *Subdivision for Modeling and Animation*. SIGGRAPH Course Notes, 2000.
4. Mario Botsch et al. *Polygon Mesh Processing*. A K Peters/CRC Press, 2010.
