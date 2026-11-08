---
title: "2D Finite Volume Solver for Heat Diffusion"
summary: "A finite volume solver for steady heat diffusion with variable conductivity, written in Python. Verified with a manufactured solution, showing second-order accuracy, and used to compare direct and iterative linear solvers."
date: 2025-01-15
featured: 5
group: cfd
status: "Completed"
context: "MSc coursework — Advanced Computational Methods for Aerospace Applications, University of Minho"
software: ["Python", "NumPy", "MATLAB Symbolic Math Toolbox", "Matplotlib"]
methods: ["Finite Volume Method", "Method of Manufactured Solutions", "Direct / Gauss–Seidel / SOR"]
tags: ["CFD", "Heat transfer", "Numerical methods", "Code verification"]
math: true
cover: "/assets/img/projects/fvm-2d-diffusion-solver/mms-sor-solution.png"
cover_fit: contain
cover_caption: "Numerical and analytical temperature fields (top), absolute and relative error (bottom). SOR solver, 320 × 160 mesh."
highlights:
  - value: "≈ 2.0"
    label: "observed order of accuracy"
  - value: "5 meshes"
    label: "from 20 × 10 to 320 × 160"
  - value: "≈ 5 ×"
    label: "faster with SOR than Gauss–Seidel"
links:
  - label: "Code on GitHub"
    url: "https://github.com/antoji03/2D-Pure-Diffusion-CFD-Solver"
  - label: "Technical report (PDF)"
    url: "https://github.com/antoji03/2D-Pure-Diffusion-CFD-Solver/blob/main/2D_Pure_Diffusion_CFD_Code_Report.pdf"
---

## Objective

Build a complete finite volume solver for the 2D steady heat diffusion equation
with spatially varying conductivity, and answer two questions:

1. Does the code converge to the exact solution, and at what rate?
2. How do direct and iterative linear solvers compare in time and memory as the
   mesh is refined?

## Governing equation

The domain is a rectangle with $$L = 2\ \text{m}$$ and $$H = 1\ \text{m}$$. With
no transient or advective terms, the energy equation reduces to

$$
\nabla \cdot \big( k(x,y)\, \nabla T \big) + F = 0
$$

where the conductivity field, with $$k_\text{ref} = 0.15$$ W/(m·K), $$k_x = 1$$
and $$k_y = 0$$, is

$$
k(x,y) = k_\text{ref} \left[ k_x \cos\!\left(\frac{2\pi x}{L}\right) + k_y \sin\!\left(\frac{\pi y}{H}\right) \right]
$$

## Method of Manufactured Solutions

To measure the true error of the code, I chose the exact solution in advance:

$$
T_\text{an}(x,y) = 50 \cos\phi + 200, \qquad \phi = \frac{4\pi x}{L} + \frac{2\pi y}{H}
$$

Substituting it into the equation gives the source term that makes it an exact
solution, $$F = -\nabla \cdot (k \nabla T_\text{an})$$. I derived this
symbolically in MATLAB:

$$
\begin{aligned}
\nabla \cdot (k \nabla T_\text{an}) ={}& \frac{400\, k_\text{ref} k_x \pi^2}{L^2} \sin\!\left(\frac{2\pi x}{L}\right) \sin\phi
- \frac{100\, k_\text{ref} k_y \pi^2}{H^2} \cos\!\left(\frac{\pi y}{H}\right) \sin\phi \\
& - 200\, k_\text{ref} \pi^2 \left( \frac{4}{L^2} + \frac{1}{H^2} \right) \cos\phi \left[ k_x \cos\!\left(\frac{2\pi x}{L}\right) + k_y \sin\!\left(\frac{\pi y}{H}\right) \right]
\end{aligned}
$$

This conductivity is a mathematical test case rather than a real material: it is
negative for $$0.5 < x < 1.5$$ and zero at $$x = 0.5$$ and $$x = 1.5$$. MMS does
not need physical coefficients, only a smooth solution that exercises every term
of the code.

## Finite volume discretisation

Integrating over a control volume and applying the divergence theorem turns the
equation into a balance of fluxes through the four faces. With central differences
for the gradients, and $$k$$ evaluated at each face, every cell gives one linear
equation:

$$
a_P T_P = a_E T_E + a_W T_W + a_N T_N + a_S T_S + S_U
$$

$$
a_E = k_e \frac{\Delta y}{\Delta x}, \quad
a_W = k_w \frac{\Delta y}{\Delta x}, \quad
a_N = k_n \frac{\Delta x}{\Delta y}, \quad
a_S = k_s \frac{\Delta x}{\Delta y}
$$

$$
a_P = a_E + a_W + a_N + a_S - S_P, \qquad S_U = F\, \Delta x \Delta y
$$

### Boundary conditions

| Boundary | Type | Neighbour coefficient | Added to S<sub>P</sub> | Added to S<sub>U</sub> |
|---|---|---|---|---|
| South | Prescribed temperature | a<sub>S</sub> = 0 | −2 k<sub>s</sub> Δx/Δy | 2 k<sub>s</sub> T<sub>an</sub>(x, 0) Δx/Δy |
| West | Prescribed temperature | a<sub>W</sub> = 0 | −2 k<sub>w</sub> Δy/Δx | 2 k<sub>w</sub> T<sub>an</sub>(0, y) Δy/Δx |
| East | Prescribed flux | a<sub>E</sub> = 0 | 0 | k<sub>e</sub> (∂T<sub>an</sub>/∂x) Δy |
| North | Prescribed flux | a<sub>N</sub> = 0 | 0 | k<sub>n</sub> (∂T<sub>an</sub>/∂y) Δx |

The factor of 2 appears because the distance from the cell centre to the boundary
face is half a cell.

## Linear solvers

The equations form a linear system $$\mathbf{A}\mathbf{T} = \mathbf{b}$$, solved
in three ways:

- **Direct:** `numpy.linalg.solve`, with $$\mathbf{A}$$ stored as a dense matrix.
- **Gauss–Seidel:** each cell is updated in turn, using the newest neighbour values.
- **SOR:** the Gauss–Seidel update is over-relaxed, here with $$\omega = 1.7$$:

$$
T_P^{(n+1)} = (1 - \omega)\, T_P^{(n)} + \omega\, \frac{\sum_{nb} a_{nb} T_{nb} + S_U}{a_P}
$$

The iterative solvers stop when the relative change between iterations is small:

$$
\frac{\lVert \mathbf{T}^{(n+1)} - \mathbf{T}^{(n)} \rVert_2}{\lVert \mathbf{T}^{(n+1)} \rVert_2} < 10^{-8}
$$

## Verification: order of accuracy

Each refinement halves the cell size, so a second-order scheme should reduce the
error by a factor of 4. The observed order is

$$
p = \log_2 \frac{e_{h}}{e_{h/2}}
$$

using the root-mean-square error against the exact solution:

| Mesh | RMS error (K) | Error ratio | Observed order p |
|---|---|---|---|
| 20 × 10 | 2.50 | – | – |
| 40 × 20 | 0.622 | 4.0 | 2.0 |
| 80 × 40 | 0.153 | 4.1 | 2.0 |
| 160 × 80 | 0.0380 | 4.0 | 2.0 |
| 320 × 160 (SOR) | 0.0133 | 2.9 | 1.5 |

The first four meshes match the expected **second-order** behaviour. The last
value comes from SOR, because the direct solver could not run on that mesh. Its
lower order is probably iterative error rather than discretisation error: the
stopping criterion measures the change per iteration, not the distance to the
converged solution, and SOR already gives a slightly larger error than the direct
solver at 160 × 80.

The report and notebooks list a different metric as "mean error",
$$\lVert \mathbf{e} \rVert_2 / N$$, which shrinks with the number of cells $$N$$
as well as with the actual error. The table converts those values to RMS,
$$\lVert \mathbf{e} \rVert_2 / \sqrt{N}$$, so that meshes can be compared fairly.

## Solver performance

Time and peak memory for each solver, from the technical report:

| Mesh | Direct | Gauss–Seidel | SOR |
|---|---|---|---|
| 20 × 10 | 0.21 s, 102 MB | 1.68 s, 101 MB | 0.49 s, 102 MB |
| 40 × 20 | 0.17 s, 161 MB | 15.2 s, 153 MB | 2.93 s, 153 MB |
| 80 × 40 | 1.42 s, 267 MB | 138 s, 181 MB | 31.0 s, 181 MB |
| 160 × 80 | 58.7 s, 1537 MB | 59 min, 292 MB | 12 min, 255 MB |
| 320 × 160 | Out of memory | – | 2 h 21 min, 710 MB |

- **The direct solver was the fastest**, but memory limited it. A dense matrix for
  320 × 160 needs about 21 GB (51 200² values of 8 bytes each), and the run failed
  for lack of memory.
- **SOR was about 4 to 5 times faster than Gauss–Seidel** on the same mesh, for the
  same stopping criterion.
- **The iterative solvers used far less memory** on fine meshes, which is why they
  were the only option at 320 × 160.

## Results

![Direct solver results](/assets/img/projects/fvm-2d-diffusion-solver/mms-direct-solution.png)
*Direct solver, 160 × 80 mesh.*

![Gauss–Seidel results](/assets/img/projects/fvm-2d-diffusion-solver/mms-gauss-seidel-solution.png)
*Gauss–Seidel, 80 × 40 mesh. Note that the colour scale of each error map changes from figure to figure.*

![SOR results](/assets/img/projects/fvm-2d-diffusion-solver/mms-sor-solution.png)
*SOR, 320 × 160 mesh.*

The error maps show jumps at $$x = 0.5$$ and $$x = 1.5$$. These are the lines where
$$k = 0$$: with the cell faces aligned to them, practically no heat crosses, so the
regions on either side are numerically decoupled.

## Limitations and next steps

- $$\omega = 1.7$$ was fixed. A better value should reduce the cost on fine meshes.
- Storing $$\mathbf{A}$$ in a sparse format would extend the direct solver to finer
  meshes.
- The iterations run in pure Python loops, so the times compare the methods but do
  not represent the speed of an optimised code.

## What I learned

- How the governing equation and each boundary condition become coefficients of a
  linear system.
- How to verify a code by measuring its order of accuracy, not only by checking
  that the contour plot looks right.
- How direct and iterative solvers trade time against memory as a mesh grows.
