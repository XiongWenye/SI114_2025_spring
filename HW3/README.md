# MATLAB Script: Demonstration of the Gibbs Phenomenon

## 1. Purpose

This MATLAB script ('Gibbs.m') serves to numerically and visually demonstrate the **Gibbs Phenomenon**. The Gibbs phenomenon describes the manner in which the Fourier series of a piecewise continuously differentiable periodic function behaves at a jump discontinuity. Specifically, the partial sums of the Fourier series exhibit oscillations near the discontinuity, which overshoot and undershoot the actual function values. A key characteristic is that the magnitude of this overshoot does not die out as more terms are added to the series; instead, it converges to a fixed percentage (approximately 9%) of the jump size, although the region where the overshoot occurs becomes narrower.

This script uses a **square wave** as the example function to illustrate this phenomenon.

## 2. How it Works

The script performs the following steps:

1.  **Parameter Definition:**
    * `L`: Defines half the period of the square wave (period = `2*L`). Set to `pi` for simplicity in Fourier series coefficients.
    * `N_terms_array`: An array specifying different numbers of terms (e.g., 5, 15, 50, 100) to be used in the Fourier series approximation. This allows for comparison of the approximation quality and the persistence of the Gibbs phenomenon.
    * `num_points`: The number of points used for plotting the functions, ensuring smooth visual representations.

2.  **Time Vector Generation:**
    * A time vector `t` is created, typically spanning slightly more than one period of the square wave, to clearly visualize its periodic nature and the behavior around discontinuities.

3.  **Ideal Square Wave Definition:**
    * The script generates an "ideal" square wave. This wave is defined to be:
        * `+1` for `0 < t < L`
        * `-1` for `-L < t < 0`
        * `0` at the points of discontinuity (`t = 0, ±L, ±2L, ...`), as the Fourier series converges to the midpoint of the jump.
    * This ideal wave serves as a reference against which the Fourier series approximations are compared.

4.  **Fourier Series Calculation:**
    * The script iterates through each number of terms specified in `N_terms_array`.
    * For each `N` (number of terms), it calculates the partial sum of the Fourier series for the square wave. The series for a square wave (odd function, amplitude 1, period `2*L` with `L=pi`) is:
        `f(t) ≈ (4/π) * Σ [ (1/n) * sin(nt) ]`
        where the sum is over odd integers `n` from `1` up to (or corresponding to) `N`.
    * The script correctly includes only the odd harmonics in the sum.

5.  **Plotting:**
    * A figure is generated to display the results.
    * The **ideal square wave** is plotted (typically as a dashed line).
    * Each **Fourier series approximation** (for different `N`) is plotted on the same axes, using different colors for clarity.
    * Lines indicating the **theoretical maximum overshoot and minimum undershoot** due to the Gibbs phenomenon are also plotted. This is approximately 1 ± 0.08949 * (Jump Height / 2). For a jump from -1 to 1 (jump height = 2), this means peaks around 1.09 and troughs around -1.09.

6.  **Output:**
    * The primary output is the plot visually demonstrating:
        * How the Fourier series approximates the square wave.
        * The characteristic overshoots and undershoots near the discontinuities (`t=0, ±π, ...`).
        * The fact that the magnitude of this overshoot does not diminish significantly with an increasing number of terms, although it becomes more localized to the discontinuity.
    * The script also prints a brief explanation of the Gibbs phenomenon to the MATLAB command window.

## 3. Expected Observations

* The Fourier series approximations will generally get closer to the flat parts of the square wave as `N` (the number of terms) increases.
* Near the jump discontinuities, each approximation will exhibit "horns" or "ears" that overshoot the `+1` value and undershoot the `-1` value.
* The height of these overshoots/undershoots will remain relatively constant (around 9% of the jump) even for a large number of terms, demonstrating the persistence of the Gibbs phenomenon.
* The oscillations will become more compressed (i.e., their "wavelength" will decrease) and confined closer to the discontinuity as `N` increases.

This script provides a clear and effective way to understand this important concept in Fourier analysis.
