# Two-Dimensional FEM Simulation of Ion Transport and pH Evolution

## Overview

This repository provides a step-by-step development and verification of a 2D finite element method (FEM) framework in Python. The project starts with the generation of triangular mesh, followed by matrix assembly and time-dependent diffusion. Then, the verification of the numerical solver by an analytical solution and convergence studies.

The resulting diffusion framework will provide the numerical basis to model coupled hydrogen-ion and hydroxide-ion transport and the evolution of pH.
## Project Motivation

Transport of ions and change of pH are important in electrochemical and biological systems. Such processes are typically described by time-dependent partial differential equations, which are not always analytically tractable, even in complicated domains.

In this work the numerical model is developed in a step-by-step manner and each component, i.e. mesh generation, FEM assembly, time integration, and solver accuracy is verified before coupled ion transport is introduced.

## Governing Diffusion Equation

The current model solves the two-dimensional diffusion equation:

The two-dimensional diffusion equation is

```math
\frac{\partial c}{\partial t}
=
D\left(
\frac{\partial^2 c}{\partial x^2}
+
\frac{\partial^2 c}{\partial y^2}
\right),
\qquad (x,y)\in\Omega.
```

where:

* $c(x,y,t)$ is the concentration;
* $D$ is the diffusion coefficient;
* $\nabla^2c$ is the two-dimensional Laplacian;
* $\Omega=[0,1]\times[0,1]$ is the computational domain.

The zero-flux boundary condition is

$$
-D\frac{\partial c}{\partial n}=0
\quad \text{on the boundary } \partial\Omega.
$$

## Finite Element Formulation

Using linear triangular basis functions, the semi-discrete FEM system is

```math
M\frac{d\mathbf{c}}{dt}
+
K\mathbf{c}
=
\mathbf{0}.
```

where $M$ is the global mass matrix and $K$ is the global stiffness matrix.

The implicit Euler method gives

```math
\left(M+\Delta t\,K\right)\mathbf{c}^{\,n+1}
=
M\mathbf{c}^{\,n}.
```

## Implemented Parts

### Part 1: Triangular Mesh Generation and Verification

A structured triangular mesh is generated on the unit-square domain.

Main features include:

* nodal-coordinate generation;
* triangular-element connectivity;
* boundary-node identification;
* signed-area calculation;
* duplicate-element detection;
* mesh-area verification;
* mesh visualization.

For $N_x=N_y=12$, the mesh contains:

* 169 nodes;
* 288 triangular elements;
* 48 unique boundary nodes;
* total mesh area equal to 1.

### Part 2: FEM Mass and Stiffness Matrix Assembly

Local mass and stiffness matrices are calculated for every linear triangular element and assembled into the global matrices.

The local mass matrix is

```math
M^{(e)}
=
\frac{A_e}{12}
\begin{bmatrix}
2 & 1 & 1 \\
1 & 2 & 1 \\
1 & 1 & 2
\end{bmatrix}.
```

The local stiffness matrix is

```math
K^{(e)}
=
D A_e G G^{T},
```

where $A_e$ is the area of the triangular element, $D$ is the diffusion coefficient, and $G$ contains the gradients of the element basis functions.

The assembled matrices are verified for:

* correct dimensions;
* symmetry;
* positive diagonal entries;
* conservation properties;
* constant nullspace of the stiffness matrix.

### Part 3: Time-Dependent Diffusion

The diffusion equation is solved using linear triangular finite elements in space and the implicit Euler method in time.

The Gaussian initial concentration is

```math
c(x,y,0)
=
\exp\left[
-\frac{(x-x_c)^2+(y-y_c)^2}{2\sigma^2}
\right].
```

Here, $(x_c,y_c)$ is the centre of the concentration peak and $\sigma$ controls its width.

The numerical results demonstrate that:

* the concentration peak decreases and spreads across the domain;
* total concentration is conserved;
* discrete energy decreases with time;
* a uniform concentration remains unchanged;
* the solution remains finite and stable.

### Part 4: Exact-Solution Verification and Convergence Analysis

The numerical solver is verified using the analytical solution

```math
c_{\mathrm{exact}}(x,y,t)
=
1+
0.25\exp(-2\pi^2Dt)
\cos(\pi x)\cos(\pi y).
```

For a mesh containing 169 nodes and 288 triangular elements:

* the discrete $L^2$ error is approximately $5.62\times10^{-4}$;
* the maximum nodal error is approximately $1.53\times10^{-3}$.

The spatial convergence rates approach 2. Therefore, the spatial error behaves approximately as

```math
\lVert e_h\rVert_{L^2}
=
O(h^2).
```

This confirms the expected second-order spatial accuracy of the linear triangular finite element method.

The temporal convergence rates approach 1. Therefore, the temporal error behaves approximately as

```math
\lVert e_{\Delta t}\rVert_{L^2}
=
O(\Delta t).
```

This confirms the expected first-order temporal accuracy of the implicit Euler method.


## Numerical Verification Summary

| Verification test               |               Result |
| ------------------------------- | -------------------: |
| Number of mesh nodes            |                  169 |
| Number of triangular elements   |                  288 |
| Total mesh area                 |                  1.0 |
| Mass-matrix symmetry error      |                  0.0 |
| Stiffness-matrix symmetry error |                  0.0 |
| Maximum diffusion mass error    | $2.16\times10^{-15}$ |
| Uniform-solution error          | $1.67\times10^{-15}$ |
| Reference discrete $L^2$ error  |  $5.62\times10^{-4}$ |
| Maximum nodal error             |  $1.53\times10^{-3}$ |
| Observed spatial order          |      Approximately 2 |
| Observed temporal order         |      Approximately 1 |

## Technologies Used

* Python
* NumPy
* SciPy
* Matplotlib
* Google Colab
* Finite Element Method
* Implicit Euler Method
* LU Factorization

## How to Run

1. Open the required notebook in Google Colab.
2. Run all cells in sequence.
3. Start with Part 1 and continue in numerical order.
4. The verification messages and figures will be generated automatically.

## Future Development

The verified diffusion solver will be extended to coupled concentrations of hydrogen and hydroxide ions.

The pH is calculated from the hydrogen-ion concentration using

```math
\mathrm{pH}
=
-\log_{10}\left(c_{\mathrm{H}^{+}}\right).
```

Future stages will include:

* different diffusion coefficients for ionic species;
* coupled hydrogen and hydroxide transport;
* reaction terms;
* electrode boundary fluxes;
* time-dependent pH distributions;
* parameter and sensitivity studies;
* numerical verification of the coupled model.

## Author

**Zainab Qadeer**

BS Mathematics, University of Engineering and Technology Lahore

Research interests: numerical PDEs, finite element methods, scientific computing, mathematical modelling, and mathematical biology.
