---
title: Analytical Model of DMD Diffraction
layout: default
comments: true
nav_order: 3
parent: Modeling DMD Diffraction in Zemax - From Analytical Theory to Implementation
---

# Zemax DLL generation

A DMD can be modeled in Zemax Sequential Mode for incoherent applications, such as DLP projectors. In this configuration, the DMD acts as a reflective grating where individual diffraction orders are isolated using the Multi-Configuration Editor. This method uses a geometrical ray trace and ignores interference, making it ideal for standard lens design and throughput studies.

For coherent light sources used in beam steering or holographic displays, light diffracts into multiple discrete orders simultaneously. Capturing this behavior requires Non-Sequential Mode (NSC). Because a DMD functions as a blazed grating with a dynamic tilt, a custom DLL is necessary to calculate diffraction efficiency relative to the mirror's specific blaze state

The following table summarizes three distinct methods for modeling a DMD in Non-Sequential Mode.


| Feature | 1. Diffractive DLL | 2. Scattering DLL<br>(Delta Approximation) | 3. Scattering DLL<br>(Full Analytical) |
| :--- | :--- | :--- | :--- |
| **DLL Type** | Diffractive DLL (non-sequential) | Surface Scatter DLL (non-sequential) | Surface Scatter DLL (non-sequential) |
| **Ray Behavior** | **Deterministic.** One input = One output (Zemax does iteration for diffraction orders) | **Probabilistic.** One input splits into discrete orders($m_x, m_y$) by the envelope weighting | **Probabilistic.** Continuous sampling; rays can land anywhere. |
| **Visual Ray Density** | **Constant.** High-power and low-power rays look identical. | **Variable.** More rays appear at high-efficiency integer orders without side lobes. | **Variable.** More rays appear at high-efficiency integer orders with side lobes |
| **Best Use Case** | Precision throughput and lens optimization. | Visualizing ray paths and mechanical clipping of orders. | stray light analysis between orders. |
| **Speed** | **Fastest.** No random searching required.<br><br>< 10s for ray trace | **Medium.** Fast because it only checks specific integers.<br><br>< 1 min for ray trace | **Slowest.** High rejection rate in Monte Carlo sampling.<br><br>> a few minutes for ray trace |

* Simulation speed can depend on PC specs. (PC spec used, CPU: Intel i3-i8130U RAM: 8GB) 

The screenshots below show the Zemax implementation across the different DLL settings. While all three methods produce a Detector Viewer output that aligns closely with my Python reference models, they differ significantly in their visual and computational behavior.

As the comparison table suggests, the Diffractive DLL produces an unnatural, constant ray density in the layout. The number of ray bundles is strictly tied to the maximum order set in the interface rather than the physical intensity. In contrast, the Scattering DLL (Delta Approximation) provides a much more intuitive result, where the ray density visually scales with the diffraction efficiency. I have hard-coded a range of ±10 orders into the DLL, which should be sufficient for the vast majority of DMD applications.

The Full Analytical scattering implementation is technically the most accurate model, as it accounts for the finite size of the mirror array and the light distribution between orders. However, the simulation speed is significantly slower due to the nature of the Monte Carlo sampling. This mode is not recommended for general design work. It should be used only for specialized cases like stray light or contrast analysis.

## Diffractive DLL

[Diff2D_DMD_250216_v2.dll](Diff2D_DMD_250216_v2.dll)

![image.png](images/image-1.png)

## Scattering DLL (Delta Approximation)

[Scattering_DMD_250217_v3.dll](Scattering_DMD_250217_v3.dll)

![image.png](images/image-2.png)

## Scattering DLL (Full Analytical Model)

[Scattering_DMD_analytical_250218_v2.dll](Scattering_DMD_analytical_250218_v2.dll)

![image.png](images/image-3.png)

## Reference Python Result

![image.png](images/image-4.png)

# Reference

A similar derivation was found in some references

- Deng et al.,  "Maximizing energy utilization in DMD-based projection lithography," Opt. Express 30, 4692-4705 (2022)
- S. M. Popoff et al., “A practical guide to digital micro-mirror devices (DMDs) for wavefront shaping” J. Phys. Photonics, 6, 043001 (2024)
- S. Scholes et al., "Structured light with digital micromirror devices: a guide to best practice," *Optical Engineering* 59(4), 041202 (2019).

References for Zemax implementation 

[Simulate 2D diffraction grating using customized diffractive DLL](https://community.zemax.com/dlls-11/simulate-2d-diffraction-grating-using-customized-diffractive-dll-113)
[Custom DLLs in OpticStudio: An overview of user-defined surfaces, objects, and other DLL types](https://optics.ansys.com/hc/en-us/articles/42661741799699-Custom-DLLs-in-OpticStudio-An-overview-of-user-defined-surfaces-objects-and-other-DLL-types)

References for DMD diffractive beam steering

- https://youtu.be/k3DWbPGgGI0?si=4dwAUiu2Rv6VboJi
- J. Chan et al., "Flash and point-and-shoot hybrid lidar by DMD-based solid-state diffractive beam and image steering," Opt. Express 33, 19650-19663 (2025)
- Nero, Gregory, et al. "Two-dimensional solid-state diffractive beam steering by digital micromirror devices." Emerging Digital Micromirror Device Based Systems and Applications XVI. Vol. 12900. SPIE, 2024.