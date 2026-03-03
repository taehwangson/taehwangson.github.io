---
title: Python Implementation
layout: default
comments: true
nav_order: 2
parent: Modeling DMD Diffraction in Zemax
---

# Python Implementation

Many diffraction grating papers are based on far-field projections because the diffraction angle calculation is a major concern.  Instead, projection onto a plane perpendicular to $k_{spec}$ and real coordinates are used for python implementation to match results with Zemax later. 

The implementation employs a vector-based planar intersection ****method that treats the observation screen as a 3D flat plane perpendicular to the specular reflection vector. By calculating a scaling factor based on the ray's directional cosine relative to the screen normal ($scale=L/(\hat k⋅ \hat z_h)$), the code accurately maps angular diffraction data into physical "mm" coordinates while naturally accounting for geometric distortion at high angles.

- Variable definitions

The simulation provides five degrees of freedom via interactive sliders, enabling a sensitivity analysis of the diffraction system:

- **Tilt Angle (θt):** Shifts the sinc envelope relative to the fixed interference grid.
- **Incidence Angles (α,β):** Rotates the incident wavevector $k_{in}$, affecting both the location of the specular reflection and the projected spacing of the grating orders.
- **Observation Distance (L):** Scales the physical "mm" footprint of the pattern on the screen.
- **Wavelength (λ):** Directly scales the angular divergence of the grating orders and the width of the central blaze.

![image.png](images/image.png)

## Python interactive diffraction simulation

To validate the analytical model, the total intensity distribution $I(q)$ was implemented in a Python-based interactive simulation. This tool allows for real-time visualization of how the diffraction pattern evolves under different physical and geometric constraints. TI DMD 7000 spec was employed.

[https://dmd-simulation-jw3t5pa5p6y7aqvsocwrzn.streamlit.app/?embed=true](https://dmd-simulation-jw3t5pa5p6y7aqvsocwrzn.streamlit.app/?embed=true)

## Python interactive phase matching condition

To illustrate the physical peak of diffraction efficiency, the simulation visualizes the Phase Matching Condition:

**$G_{mn}=q_{blaze,∥}$**

The implementation distinguishes between two variables:

1. **The Lattice (G*mn*):** Represented by red dots, these fixed points define the discrete angles where constructive interference is possible based on the grating equation: $k_{out,\parallel}=k_{in,\parallel}+2\pi/p(m,n)$ .
2. **The Blaze Center (q*blaze*):** Represented by the blue dot, this tracks the specular reflection direction: **$k_{spec}=k_{in}−2(k_{in}⋅\hat {n}) \hat {n}$**

By adjusting the sliders, the user can observe how the energy envelope (the blue dot) shifts across the stationary grating orders. Maximum efficiency is reached when the blue dot overlaps with a red dot, satisfying the phase-matching condition for a specific (*m*,*n*) order.

[https://dmd-simulation-mlbegwhffsk9tjetaaaqz5.streamlit.app/?embed=true](https://dmd-simulation-mlbegwhffsk9tjetaaaqz5.streamlit.app/?embed=true)
