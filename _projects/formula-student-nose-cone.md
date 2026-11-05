---
title: "Formula Student Nose Cone Aerodynamics"
summary: "CFD comparison of four nose cone designs for the Formula Student University of Minho car, built with a parametric CAD and simulation workflow. It shows the trade-off between downforce and drag: from Mark III to Mark VI, drag falls 12% and downforce falls 35%."
date: 2026-11-05
status: "Completed"
context: "Formula Student University of Minho — Nose cone aerodynamics"
software: ["Autodesk Inventor", "Ansys SpaceClaim", "Ansys Fluent"]
methods: ["Steady RANS", "k-ω SST", "Parametric design study", "Mesh independence study"]
tags: ["CFD", "Aerodynamics", "Formula Student", "Design study"]
math: true
cover: "/assets/img/projects/formula-student-nose-cone/mark-iii-pressure.png"
cover_fit: contain
cover_caption: "Pressure field around the Mark III nose cone at 10 m/s. Each pressure figure on this page has its own colour scale."
highlights:
  - value: "4"
    label: "designs compared (Mark III to VI)"
  - value: "0.79"
    label: "highest C_L/C_D (Mark III)"
  - value: "−12%"
    label: "C_D from Mark III to Mark VI"
  - value: "−35%"
    label: "C_L from Mark III to Mark VI"
---

## Objective

The goal was to understand how the shape of the nose cone, especially the height of
its tip, changes the aerodynamic forces on the car, and to choose a design with these
numbers in hand. The study followed an iterative loop: change the CAD, run the CFD,
compare the results, and change the CAD again.

## Process

1. **Parametric CAD (Autodesk Inventor).** The main variable was the tip height,
   controlled through reference sketches and driven dimensions, so a new variant could
   be generated quickly.
2. **Geometry preparation (Ansys SpaceClaim).** Each design was exported as STEP, then
   inspected, simplified and repaired until watertight. All designs used a common
   external flow domain. Symmetry was not used, so asymmetric flow structures could
   still develop.
3. **Meshing.** An unstructured polyhedral/tetrahedral mesh with near-wall prism layers.
4. **Simulation (Ansys Fluent).** The same mesh settings, schemes, convergence criteria
   and ground distance were used for every design, so differences in the results come
   from the geometry.

## Simulation setup

| Setting | Value |
|---|---|
| Solver | Pressure-based, steady |
| Turbulence model | k-ω SST |
| Inlet | Uniform velocity, 10 m/s |
| Inlet turbulence | 1% intensity, viscosity ratio 1.5 |
| Outlet | Pressure outlet, zero gauge pressure |
| Walls | No-slip |
| Spatial discretisation | Second order for all variables |
| Pressure–velocity coupling | SIMPLE or coupled, depending on stability |
| Convergence | Residuals below 10⁻⁵, with stable drag coefficient |

### Near-wall mesh and mesh independence

The k-ω SST model was used with the boundary layer resolved down to the wall, so the
first prism layer was sized for a target of $$y^+ \approx 1$$:

$$
y^+ = \frac{\rho\, u_\tau\, y}{\mu}
$$

The mesh used 12 to 15 prism layers, with a growth ratio of 1.15 to 1.2, to blend
smoothly into the core mesh. A check at three refinement levels showed that the
integrated drag coefficient of the selected mesh differed by less than 1% from the next
finer level.

## Force coefficients

The designs are compared through dimensionless coefficients, with $$\rho$$ the air
density, $$U$$ the inlet velocity and $$A$$ the reference area:

$$
C_L = \frac{L}{\tfrac{1}{2} \rho U^2 A}, \qquad
C_D = \frac{D}{\tfrac{1}{2} \rho U^2 A}, \qquad
C_p = \frac{p - p_\infty}{\tfrac{1}{2} \rho U^2}
$$

Here, a positive $$C_L$$ means downforce.

## Results

| Design | C<sub>L</sub> (downforce) | C<sub>D</sub> | C<sub>L</sub>/C<sub>D</sub> |
|---|---|---|---|
| Mark III | 0.270 | 0.340 | 0.79 |
| Mark IV | 0.198 | 0.307 | 0.64 |
| Mark V | 0.238 | 0.329 | 0.72 |
| Mark VI | 0.175 | 0.299 | 0.59 |

- **Downforce and drag rise together.** Ranking the designs by C<sub>L</sub> or by
  C<sub>D</sub> gives the same order: Mark VI, IV, V and III.
- **There is a trade-off.** Mark VI has the lowest drag, 12% below Mark III, but it also
  has 35% less downforce. Mark III has the highest downforce and the highest
  C<sub>L</sub>/C<sub>D</sub>, but also the highest drag.
- **Which is best depends on the car.** The choice depends on how the nose cone works
  with the rest of the aerodynamic package, which this study does not include.

### Mark III

The pressure field is at the top of this page. Mark III has a large inclined surface
under its tip and a sharp corner where that surface meets the flat underside. The plotted
pressure there reaches about −142 Pa, the strongest suction of the four designs.

### Mark IV

![Mark IV CAD](/assets/img/projects/formula-student-nose-cone/mark-iv-cad.png)
*Mark IV geometry in Autodesk Inventor.*

![Mark IV pressure field](/assets/img/projects/formula-student-nose-cone/mark-iv-pressure.png)
*Mark IV pressure field. C<sub>L</sub> = 0.198, C<sub>D</sub> = 0.307.*

### Mark V

![Mark V CAD](/assets/img/projects/formula-student-nose-cone/mark-v-cad.png)
*Mark V geometry in Autodesk Inventor.*

![Mark V pressure field](/assets/img/projects/formula-student-nose-cone/mark-v-pressure.png)
*Mark V pressure field. C<sub>L</sub> = 0.238, C<sub>D</sub> = 0.329.*

### Mark VI

![Mark VI CAD](/assets/img/projects/formula-student-nose-cone/mark-vi-cad.png)
*Mark VI geometry in Autodesk Inventor.*

![Mark VI pressure field](/assets/img/projects/formula-student-nose-cone/mark-vi-pressure.png)
*Mark VI pressure field. C<sub>L</sub> = 0.175, C<sub>D</sub> = 0.299.*

## Sanity check

For standard air at 10 m/s, the dynamic pressure is

$$
\tfrac{1}{2} \rho U^2 = \tfrac{1}{2} \times 1.225 \times 10^2 \approx 61\ \text{Pa}
$$

At the stagnation point, $$C_p$$ should be close to 1. The peak pressures in the figures,
between 58 and 60 Pa, give $$C_p \approx 0.95$$ to $$0.98$$, as expected.

## Limitations and next steps

- **Isolated part.** The nose cone ends in a flat rear face, followed by a wake. On the
  car, this face connects to the rest of the body, so the absolute C<sub>D</sub> values
  are not those of the installed nose cone. The comparison between designs is more
  meaningful than the absolute numbers.
- **No experimental validation yet.** The results come from CFD only. The planned next
  steps are transient simulations, wind tunnel validation and structural integration.
- **One speed.** All results are at 10 m/s, so they do not show how the coefficients
  change with Reynolds number.

## What I learned

- How to link parametric CAD and CFD into a fast design loop.
- How to keep a comparison fair by holding the mesh settings, schemes and convergence
  criteria constant.
- How to read force coefficients as a trade-off rather than looking for one winning
  number.
