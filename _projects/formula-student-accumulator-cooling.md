---
title: "Air Cooling of a Formula Student Accumulator"
summary: "2D and 3D CFD design of the passive air cooling for a 432-cell battery in an electric Formula Student car, from cell spacing and wall openings to the power and airflow limits at 35 °C."
date: 2025-06-15
status: "Completed"
context: "Formula Student University of Minho — Accumulator cooling"
software: ["Ansys Fluent", "Ansys Meshing", "Excel"]
methods: ["Conjugate heat transfer", "2D and 3D parametric studies", "Volumetric heat source"]
tags: ["CFD", "Heat transfer", "Battery cooling", "Formula Student"]
math: true
cover: "/assets/img/projects/formula-student-accumulator-cooling/opening-square-temperature-3d.jpg"
cover_fit: contain
cover_caption: "3D cell temperature for the chosen square opening, with a maximum of 49.8 °C at 26.85 °C ambient, 10 m/s inlet speed."
highlights:
  - value: "432"
    label: "Molicel P45B cells, cooled by air only"
  - value: "5 mm"
    label: "chosen spacing between cells"
  - value: "30 kW"
    label: "Endurance power chosen by the team"
  - value: "8 m/s"
    label: "minimum inlet speed from the 2D study"
---

## Objective

The accumulator powers the car's electric drivetrain, and its cells must stay below
60 °C or the car has to shut down. This was the team's second accumulator, and the goal
was to improve on the previous year's design while keeping the system cost-effective.

The system uses no fans or pumps. Air enters through a sidepod while the car is moving
and flows through the battery modules. The main change from the previous year was a
**staggered** array of cells instead of an inline one, to increase turbulence and the
convection coefficient.

## Requirements

The design was driven by three points in the Formula Student rules:

- **Coolant:** air is one of the permitted coolants.
- **Wall openings:** holes in an accumulator wall must stay below 25% of that wall's
  area, so every opening had to fit a strict area budget.
- **Temperature:** the maximum cell temperature is 60 °C, or the datasheet limit if
  lower. The temperature of at least 30% of the lithium cells must be monitored.

## Modelling the heat from the cells

Each cell was modelled as a solid with a uniform volumetric heat source, calculated from
the power used by the car, the number of cells and the cell volume:

$$
\dot{q} = \frac{P\,(1 - \eta)}{N\, V_\text{cell}}, \qquad V_\text{cell} = \frac{\pi}{4} d^2 h
$$

with efficiency $$\eta = 0.94$$, $$N = 432$$ cells and a cell diameter and height of
21.55 mm and 70 mm, which give $$V_\text{cell} \approx 2.55 \times 10^{-5}\ \text{m}^3$$.

![Molicel P45B material properties in Fluent](/assets/img/projects/formula-student-accumulator-cooling/cell-material-properties.png)
*Cell properties used in Fluent, from the manufacturer's data: density 2887 kg/m³, specific heat 888 J/(kg·K) and conductivity 0.83 W/(m·K).*

## Step 1: Spacing between cells (2D)

The first study compared five cell layouts in 2D, changing only the distances between
cells. Three kept 20 mm between the centres of consecutive cells and changed the vertical
gap (3, 6 and 9 mm). Two set the same vertical and diagonal gap (5 and 6 mm).

**Boundary conditions:**

| Setting | Value |
|---|---|
| Inlet | Velocity, 10 m/s |
| Ambient temperature | 26.85 °C (300 K) |
| Outlet | Pressure outlet, 0 Pa |
| Cell heat source | 184,954 W/m³, at peak power (about 34 kW) |

![3 mm spacing geometry](/assets/img/projects/formula-student-accumulator-cooling/spacing-3mm-geometry.png)
*Geometry for the 3 mm spacing, with named selections for the inlet, outlet, walls and cells.*

![6 mm spacing geometry](/assets/img/projects/formula-student-accumulator-cooling/spacing-6mm-geometry.jpg)
*Geometry for the 6 mm vertical spacing.*

![9 mm spacing geometry](/assets/img/projects/formula-student-accumulator-cooling/spacing-9mm-geometry.jpg)
*Geometry for the 9 mm vertical spacing.*

**Mesh:** the 2D mesh was built in Ansys Meshing, with local refinement at the edges of
the cells, where the air flows over the surface. This gives better accuracy for the same
computing power than refining the whole domain.

![2D mesh](/assets/img/projects/formula-student-accumulator-cooling/mesh-2d.png)
*2D mesh, refined around every cell.*

### Results

| Layout | Max. cell temperature | Max. air speed |
|---|---|---|
| 3 mm vertical | 50.49 °C | 61.5 m/s |
| 6 mm vertical | 51.01 °C | 47.0 m/s |
| 9 mm vertical | 50.91 °C | 42.3 m/s |
| 5 mm vertical and diagonal | 50.89 °C | 52.9 m/s |
| 6 mm vertical and diagonal | 48.30 °C | 54.4 m/s |

The 5 mm layout used a 3 mm wall between modules, while the others used a 10 mm wall.

#### 3 mm vertical

![Temperature, 3 mm spacing](/assets/img/projects/formula-student-accumulator-cooling/spacing-3mm-temperature.jpg)
*Cell temperature. Only the middle cell of the last row reaches the maximum.*

![Air speed, 3 mm spacing](/assets/img/projects/formula-student-accumulator-cooling/spacing-3mm-velocity.jpg)
*Air speed. The flow splits into four zones and speeds up in the narrow gaps.*

#### 6 mm vertical

![Temperature, 6 mm spacing](/assets/img/projects/formula-student-accumulator-cooling/spacing-6mm-temperature.png)
*Cell temperature.*

![Air speed, 6 mm spacing](/assets/img/projects/formula-student-accumulator-cooling/spacing-6mm-velocity.png)
*Air speed.*

#### 9 mm vertical

![Temperature, 9 mm spacing](/assets/img/projects/formula-student-accumulator-cooling/spacing-9mm-temperature.png)
*Cell temperature.*

![Air speed, 9 mm spacing](/assets/img/projects/formula-student-accumulator-cooling/spacing-9mm-velocity.png)
*Air speed.*

#### 5 mm vertical and diagonal

![Temperature, 5 mm spacing](/assets/img/projects/formula-student-accumulator-cooling/spacing-5mm-temperature.png)
*Cell temperature.*

![Air speed, 5 mm spacing](/assets/img/projects/formula-student-accumulator-cooling/spacing-5mm-velocity.png)
*Air speed.*

#### 6 mm vertical and diagonal

![Temperature, 6 mm vertical and diagonal spacing](/assets/img/projects/formula-student-accumulator-cooling/spacing-6mm-diagonal-temperature.jpg)
*Cell temperature, the lowest of the five layouts.*

![Air speed, 6 mm vertical and diagonal spacing](/assets/img/projects/formula-student-accumulator-cooling/spacing-6mm-diagonal-velocity.jpg)
*Air speed.*

### Decision: 5 mm

Thermal performance alone pointed to 6 mm vertical and diagonal, but that made the
accumulator too long. It was unclear whether a module with 3 mm spacing could be
manufactured. **The 5 mm layout was the best of the remaining options.**

## Step 2: Shape of the wall opening (3D)

Next, 3D simulations compared three openings, with the same geometry at the entry,
middle and exit walls, and the same boundary conditions as before. The module wall has
an area of 8243 mm², which allows a cutout of up to 2060.7 mm².

| Opening | Cutout area | Share of wall | Max. cell temperature |
|---|---|---|---|
| Hexagonal grills (9) | 1955 mm² | 23.7% | 51.18 °C |
| **Square cut, 66.38 × 31 mm** | **2058 mm²** | **25.0%** | **49.82 °C** |
| Circle cut, Ø 51 mm | 2043 mm² | 24.8% | 51.89 °C |

In every case, the hottest part of each cell is the end inside the accumulator wall,
where the air cannot reach it, while the exposed centre stays much cooler.

### Hexagonal grills

![Hexagonal grill sketch](/assets/img/projects/formula-student-accumulator-cooling/opening-hex-sketch.png)
*Sketch dimensions of the grills.*

![Module with hexagonal grills](/assets/img/projects/formula-student-accumulator-cooling/opening-hex-module.jpg)
*Module with the hexagonal grills.*

![Temperature with hexagonal grills](/assets/img/projects/formula-student-accumulator-cooling/opening-hex-temperature.jpg)
*Cell temperature. The centre of the cells averages about 35 °C and the ends about 48 °C.*

![Airflow through the grills](/assets/img/projects/formula-student-accumulator-cooling/opening-hex-airflow.png)
*Airflow through the grills.*

The grills stayed within the limit, but the other openings promised better results and
the grills would have been difficult to validate structurally.

### Square cut

![Module with the square cut](/assets/img/projects/formula-student-accumulator-cooling/opening-square-module.jpg)
*Module with the square cut, aligned with the highest and lowest cells. The 3D temperature result is at the top of this page.*

![2D view of the airflow with the square cut](/assets/img/projects/formula-student-accumulator-cooling/opening-square-airflow.jpg)
*Airflow over the cells with the square cut.*

### Circle cut

![Module with the circle cut](/assets/img/projects/formula-student-accumulator-cooling/opening-circle-module.jpg)
*Module with the circle cut, centred on the wall.*

![Temperature with the circle cut](/assets/img/projects/formula-student-accumulator-cooling/opening-circle-temperature.jpg)
*Cell temperature with the circle cut.*

![Airflow with the circle cut](/assets/img/projects/formula-student-accumulator-cooling/opening-circle-airflow.png)
*Airflow inside the module with the circle cut.*

### Decision: square cut

The square cut gave the lowest maximum temperature, about 1.4 °C below the grills and
2.1 °C below the circle.

## Step 3: Maximum power for Endurance (3D)

With the geometry fixed, the question became how much power the car could use. The study
assumed a critical case: 35 °C ambient, constant maximum power and 94% efficiency, with
16 m/s at the inlet, a 0 Pa outlet and a backflow temperature of 35 °C.

| Power | Heat source | Max. cell temperature | Rise above ambient |
|---|---|---|---|
| 30 kW | 163,195 W/m³ | **63.57 °C** | 28.6 °C |
| 25 kW | 136,078 W/m³ | 57.71 °C | 22.7 °C |
| 20 kW | 108,797 W/m³ | 54.11 °C | 19.1 °C |

For a fixed airflow, the temperature rise should grow roughly with the power, and these
results follow that trend.

![30 kW, section view](/assets/img/projects/formula-student-accumulator-cooling/power-30kw-section.jpg)
*30 kW, section view. The hottest cells are the last ones in the second module.*

![30 kW, 3D view](/assets/img/projects/formula-student-accumulator-cooling/power-30kw-3d.jpg)
*30 kW, 3D view.*

![25 kW, section view](/assets/img/projects/formula-student-accumulator-cooling/power-25kw-section.jpg)
*25 kW, section view.*

![25 kW, 3D view](/assets/img/projects/formula-student-accumulator-cooling/power-25kw-3d.jpg)
*25 kW, 3D view.*

![20 kW, section view](/assets/img/projects/formula-student-accumulator-cooling/power-20kw-section.jpg)
*20 kW, section view.*

![20 kW, 3D view](/assets/img/projects/formula-student-accumulator-cooling/power-20kw-3d.jpg)
*20 kW, 3D view.*

### Decision: 30 kW

The 30 kW case exceeded the 60 °C limit by 3.6 °C in the simulation. The team still chose
30 kW, for two reasons: the case assumed constant maximum power, and the model could not
capture every effect of the real airflow from the sidepod into the module. This judgement
would need testing on the car to confirm it, as the next section explains.

## Step 4: Minimum inlet speed (2D)

The last study looked for the lowest air speed that keeps the cells below 60 °C at 30 kW
and 35 °C ambient. It used 2D simulations because they are simpler.

| Inlet speed | Max. cell temperature |
|---|---|
| 16 m/s | 50.85 °C |
| 14 m/s | 52.15 °C |
| 10 m/s | 56.02 °C |
| **8 m/s** | **59.23 °C** |
| 7 m/s | 61.49 °C |

**In this 2D study, the minimum inlet speed is 8 m/s.**

![16 m/s](/assets/img/projects/formula-student-accumulator-cooling/speed-16ms.jpg)
*16 m/s. The hottest cells are the last ones reached by the air.*

![14 m/s](/assets/img/projects/formula-student-accumulator-cooling/speed-14ms.jpg)
*14 m/s.*

![10 m/s](/assets/img/projects/formula-student-accumulator-cooling/speed-10ms.jpg)
*10 m/s.*

![8 m/s](/assets/img/projects/formula-student-accumulator-cooling/speed-8ms.jpg)
*8 m/s, the last case below 60 °C.*

![7 m/s](/assets/img/projects/formula-student-accumulator-cooling/speed-7ms.jpg)
*7 m/s, above the limit.*

### 2D and 3D do not agree

At the same conditions (30 kW, 35 °C, 16 m/s), the 3D model predicts 63.6 °C and the 2D
model 50.8 °C, a difference of almost 13 °C. A 2D section cannot represent the cell ends
inside the walls, and these are the hottest regions in the 3D results. So the 2D model is
useful for comparing speeds, but 8 m/s should not be read as a guaranteed safe limit.

## Could fans help?

The volume flow through the square opening at the minimum speed is

$$
\dot{V} = U A = 8\ \text{m/s} \times 2.06 \times 10^{-3}\ \text{m}^2 \approx 0.0165\ \text{m}^3/\text{s}
$$

| Fan size | Frontal area | Typical flow rate |
|---|---|---|
| 30 × 30 mm | about 700 mm² | 0.0007 – 0.0014 m³/s |
| 40 × 40 mm | about 1600 mm² | 0.0014 – 0.0024 m³/s |
| 50 × 50 mm | about 2500 mm² (over the area limit) | 0.0024 – 0.0042 m³/s |

The fans that fit the opening deliver at least about six times less air than the car's
motion. They would also obstruct the flow when inactive, draw electrical power and add
failure points. **Fans were therefore not used while driving.** They could only be
justified with the car stationary, for example during testing.

## Could a valve keep water out?

The team also studied a valve with a porous PTFE film, to stop water from entering the
battery. Fluent gave a mass flow of 0.1 kg/s at 10 m/s, which with an air density of
1.2 kg/m³ gives the required flow:

$$
\dot{V} = \frac{\dot{m}}{\rho} = \frac{0.1}{1.2} \approx 0.083\ \text{m}^3/\text{s} = 4980\ \text{L/min}
$$

At 65 mbar, the supplier's data gives 1.6 L/min per cm² of film, so the area needed is

$$
A = \frac{4980}{1.6} \approx 3112\ \text{cm}^2
$$

![PTFE film characterisation](/assets/img/projects/formula-student-accumulator-cooling/valve-ptfe-film.jpg)
*Airflow through the porous film as a function of pressure difference, from the supplier.*

This area was far too large, and **the valve was dropped**. The real pressure available
would also be far below 65 mbar: the dynamic pressure of air at 10 m/s is only about
0.6 mbar, which makes the film even less viable.

## Limitations and next steps

- **No experimental validation:** the results come from CFD only, so temperature
  measurements on the car would be the key next step, especially for the 30 kW decision.
- **Simplified heat source:** the cells dissipate uniformly, at constant maximum power.
  A real Endurance run varies, so a transient model of a lap would be more realistic.
- **Inlet condition:** the speed is imposed at the inlet of the domain, so simulating the
  sidepod would show how much air actually reaches the module.

## Takeaways

- Passive air cooling works well within strict limits on area and packaging, but each
  gain has to be weighed against manufacturing and structure.
- Using simple 2D models for fast comparisons and 3D models for final checks saves time,
  as long as the gap between them is understood.
- A thermal limit near the simulated result is a reason to plan testing, not only to
  refine the model.
