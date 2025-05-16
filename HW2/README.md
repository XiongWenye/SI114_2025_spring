# MATLAB Scripts for 1D Finite Element Method (FEM) Analysis

This repository contains three MATLAB scripts that solve a one-dimensional Boundary Value Problem (BVP) using the Finite Element Method. The problem is defined as:

$$- \frac{d}{dx} \left[ C(x) \frac{du}{dx} \right] = 1, \quad \text{for } x \in (0,1)$$

with boundary conditions $u(0) = 0$ and $u(1) = 0$.

The coefficient $C(x)$ is a piecewise function:
$$
C(x) = \begin{cases}
1 & \text{if } x < 1/2 \\
1/2 & \text{if } x \ge 1/2
\end{cases}
$$

The scripts demonstrate different aspects of the FEM solution, including basic setup, handling material interfaces, and visualizing results for varying mesh densities.

## Scripts Overview

1.  **`problem1_part1.m`**: Solves the BVP with a fixed number of elements (n=4) and a simplified approach to handling the material interface.
2.  **`problem1_part2.m`**: Calculates the solution at specific points (x=1/4 and x=3/4) for different numbers of elements (n=4, 8, 1000), employing a more accurate interface handling technique.
3.  **`problem1_part3.m`**: Plots the FEM solutions for different numbers of elements (n=4, 8, 1000) using the improved interface handling and saves the resulting figure.

---

## `problem1_part1.m`

### Purpose
This script provides a basic FEM solution to the BVP using a coarse mesh with 4 linear elements. It demonstrates the fundamental steps of FEM: grid generation, assembly of the global stiffness matrix and force vector, application of boundary conditions, and solving the resulting linear system.

### How it Works
1.  **Initialization**:
    * Sets the number of elements `n = 4`.
    * Calculates the element size `h = 1/n`.
    * Generates `n+1` equally spaced grid points `x` from 0 to 1.
    * Initializes the global stiffness matrix `A` and force vector `f` to zeros.

2.  **Assembly**:
    * Iterates through each of the `n` elements.
    * **Interface Handling**: For the element that spans across $x = 0.5$ (where $C(x)$ changes), it uses a simple average value $C = 0.75$. For other elements, $C=1$ or $C=0.5$ is used based on their position relative to $x=0.5$.
    * Calculates the local stiffness matrix `A_local` for a linear element: $C/h \begin{pmatrix} 1 & -1 \\ -1 & 1 \end{pmatrix}$.
    * Calculates the local force vector `f_local` due to the distributed source term (1): $h/2 \begin{pmatrix} 1 \\ 1 \end{pmatrix}$.
    * Assembles `A_local` and `f_local` into the global `A` and `f`.

3.  **Boundary Conditions**:
    * Applies the Dirichlet boundary conditions $u(0)=0$ and $u(1)=0$ by reducing the system of equations. The first and last rows/columns of `A` and corresponding entries in `f` are effectively removed, and the solution is sought for the internal nodes.

4.  **Solve**:
    * Solves the reduced linear system `A_reduced * u_reduced = f_reduced` for `u_reduced`.
    * Reconstructs the full solution vector `u` by adding back the zero boundary values.

5.  **Output**:
    * Displays the global stiffness matrix `A`.
    * Displays the global force vector `f`.
    * Displays the full solution vector `u`.
    * Prints the solution values at the internal nodes: $u(x_i)$ for $i=2, \dots, n$.

---

## `problem1_part2.m`

### Purpose
This script calculates and displays the FEM solution at two specific points of interest, $x = 1/4$ and $x = 3/4$, for varying mesh densities (n = 4, 8, and 1000 elements). It introduces a more accurate method for handling the material interface within an element.

### How it Works
1.  **Initialization**:
    * Defines an array `n_values = [4, 8, 1000]` for the number of elements.
    * Defines points of interest `poi = [0.25, 0.75]`.
    * Initializes a `results` matrix to store $u(1/4)$ and $u(3/4)$ for each `n`.

2.  **Loop over `n_values`**:
    * For each `n` in `n_values`:
        * Generates grid points `x` and initializes `A` and `f` as in `problem1_part1.m`.
        * **Improved Interface Handling**:
            * If an element `[x_left, x_right]` contains the interface at $x_{mid} = 0.5$:
                * The element is effectively split at $x_{mid}$.
                * The contribution to the local stiffness matrix and force vector is calculated separately for the segment $[x_{left}, x_{mid}]$ (with $C=1$) and the segment $[x_{mid}, x_{right}]$ (with $C=0.5$). These contributions are then summed.
                * The length of each sub-segment is used in the calculation of its respective stiffness and force contributions.
            * For elements not containing the interface, `A_local` and `f_local` are calculated as in `problem1_part1.m`.
        * Assembles global `A` and `f`.
        * Applies boundary conditions and solves for `u` as in `problem1_part1.m`.

3.  **Interpolation**:
    * For each point `p` in `poi`:
        * Finds the element `[x(i), x(i+1)]` that contains `p`.
        * Uses linear interpolation to find $u(p)$: $u(p) = (1-\alpha)u(x_i) + \alpha u(x_{i+1})$, where $\alpha = (p - x_i)/h$.
        * Stores the interpolated value in the `results` matrix.

4.  **Output**:
    * Displays the computed values of $u(1/4)$ and $u(3/4)$ for each `n`.

---

## `problem1_part3.m`

### Purpose
This script visualizes the FEM solutions for different mesh densities (n = 4, 8, and 1000 elements). It uses the more accurate interface handling method from `problem1_part2.m` and plots the solutions on a single graph for comparison.

### How it Works
1.  **Initialization**:
    * Defines `n_values = [4, 8, 1000]`.
    * Defines cell arrays `colors` for plot line styles and `legends` for legend entries.
    * Creates a new figure and uses `hold on` to overlay plots.

2.  **Loop over `n_values`**:
    * For each `n` in `n_values`:
        * Sets up and solves the FEM system for `u(x)` using the same logic as `problem1_part2.m`, including the improved interface handling.
        * **Plotting**:
            * Plots `u` versus `x` using the specified color and marker style.
            * For `n = 1000`, markers are plotted less frequently (every 100th point) to avoid clutter.
            * Adds a legend entry for the current `n`.

3.  **Figure Enhancements**:
    * Plots a vertical dashed black line at $x = 0.5$ to clearly mark the material interface.
    * Sets `xlabel`, `ylabel`, and `title` for the plot.
    * Adds a legend to the plot, placed at the 'best' location.
    * Turns the grid on.
    * Adds a text annotation near the interface line.
    * Uses `hold off` after all plots are done.

4.  **Output**:
    * Displays the plot in a MATLAB figure window.
    * Saves the figure in the current directory.
---
