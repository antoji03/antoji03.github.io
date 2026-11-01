---
title: "Convective Heat Transfer in Poiseuille Flow"
summary: "Finite volume solver for the convection–diffusion energy equation in laminar channel flow, with viscous dissipation and parametric studies in Péclet and Brinkman number."
date: 2025-08-02
status: "Completed"
context: "Personal project — numerical methods"
software: ["Python", "NumPy", "SciPy", "Matplotlib"]
methods: ["Finite Volume Method", "Convection–diffusion", "GMRES", "Mesh independence study"]
tags: ["CFD", "Heat transfer", "Convection", "Numerical methods"]
cover: "/assets/img/projects/fvm-poiseuille-heat-transfer/temperature-pe100.png"
cover_fit: contain
cover_caption: "Temperature field for Pe = 100: hot fluid (300 °C) enters a channel with walls at 200 °C."
highlights:
  - value: "Pe 1 → 100"
    label: "Péclet number range studied"
  - value: "160 × 80"
    label: "finest mesh in independence study"
  - value: "Direct + GMRES"
    label: "linear solvers"
links:
  - label: "Code on GitHub"
    url: "https://github.com/antoji03/CFD.FVM-Poiseuille-Heat-Transfer"
---

## Objective

Simulate how a hot fluid cools as it flows through a channel with colder
walls. This is the basic problem behind heat exchangers and cooling passages.
The solver handles the **steady convection–diffusion energy equation** for
fully developed laminar (Poiseuille) flow between parallel plates, including
heating by **viscous dissipation**.

## Setup

- **Flow:** fully developed parabolic velocity profile between two plates.
- **Thermal boundary conditions:** inlet at 300 °C, walls at 200 °C.
- **Discretisation:** finite volume method on a structured 2D mesh.
- **Linear solvers:** direct (`numpy.linalg.solve`) and iterative
  **GMRES** (`scipy.sparse.linalg.gmres`) on a sparse matrix.
- **Mesh independence:** from 20 × 10 up to 160 × 80 cells.

## Results: effect of the Péclet number

The Péclet number compares heat carried by the flow (convection) with heat
spread by conduction.

### Pe = 1

![Temperature field, Pe = 1](/assets/img/projects/fvm-poiseuille-heat-transfer/temperature-pe1.png)
*Conduction dominates. The fluid reaches the wall temperature within a few percent of the channel length.*

![Temperature profiles across the channel, Pe = 1](/assets/img/projects/fvm-poiseuille-heat-transfer/profiles-pe1.png)
*Temperature profiles at ten stations along the channel. Every station after the inlet is already at the wall temperature.*

### Pe = 10

![Temperature field, Pe = 10](/assets/img/projects/fvm-poiseuille-heat-transfer/temperature-pe10.png)
*The thermal entrance region grows.*

![Temperature profiles across the channel, Pe = 10](/assets/img/projects/fvm-poiseuille-heat-transfer/profiles-pe10.png)
*The centreline is still about 13 °C above the wall temperature at x/L ≈ 0.1.*

### Pe = 100

![Temperature field, Pe = 100](/assets/img/projects/fvm-poiseuille-heat-transfer/temperature-pe100.png)
*Convection dominates. Hot fluid persists along the centreline over most of the channel.*

![Temperature profiles across the channel, Pe = 100](/assets/img/projects/fvm-poiseuille-heat-transfer/profiles-pe100.png)
*The near-uniform inlet profile develops into a parabolic shape as the thermal boundary layers merge.*

## Viscous dissipation

With a non-zero **Brinkman number**, friction in the fluid adds heat,
mostly near the walls where the shear is highest. The solver can switch
this source term on and off. The case below has it switched off (Br = 0).

![Temperature field, Br = 0](/assets/img/projects/fvm-poiseuille-heat-transfer/temperature-br0.png)
*Temperature field with viscous dissipation switched off (Br = 0).*

![Temperature profiles across the channel, Br = 0](/assets/img/projects/fvm-poiseuille-heat-transfer/profiles-br0.png)
*Temperature profiles along the channel for Br = 0.*

## What I learned

- Discretising the convective term, and how the balance between
  convection and diffusion changes as the Péclet number grows.
- Using sparse matrices and Krylov solvers (GMRES) instead of dense direct
  solves as the mesh is refined.
- Reading dimensionless groups (Pe, Br) physically. The same reasoning
  applies to cooling passages and heat exchangers in compressors.
