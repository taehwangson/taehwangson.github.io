---
title: Microscopy Objective Desensitization 
layout: default
comments: true
nav_order: 1
parent: Advanced Lens Design 
---


# Microscopy Objective Desensitization 
## Goal

Optical imaging system design is typically performed in tools such as Zemax or CODE V to achieve optimal nominal performance that meets system specifications. However, the actual (as-built) performance is often significantly degraded by fabrication and assembly tolerances.  
  
A common industrial approach to mitigate this is to include multi-configuration optimization with perturbed elements. While effective, this approach introduces a large number of variables, slows convergence, and makes it difficult to interpret the physical origin of improved tolerance performance due to its black-box nature.  
  
In contrast, this work explores **aberration-based desensitization**, where the goal is not to directly optimize perturbed systems, but to **reshape the nominal aberration structure** such that the system becomes inherently less sensitive to manufacturing errors. The approach is demonstrated on a high-NA objective lens.

## Aberration-based desensitization

Axially symmetric optical systems assume perfect alignment. Under this assumption, classical Seidel aberration correction is valid and sufficient. However, in practice, fabrication and assembly errors such as **tilt and decenter break axial symmetry**, transforming the system into a **plane-symmetric system**. **Uniform coma (UC)** and  **Linear astigmatism (LA)** are known to be the dominant contributors to image degradation under perturbation.

These aberrations grow strongly with small perturbations and are not adequately controlled in traditional Seidel-based optimization. Therefore, instead of only minimizing nominal aberrations, it is critical to **minimize sensitivity to perturbation-induced aberrations**, particularly UC and LA.

Referring to the [paper](https://doi.org/10.1364/AO.443758), the sensitivities of UC and LA are explicitly incorporated into the merit function. Additionally, the Seidel aberration-based RMS wavefront estimate $\sigma_{W}^2$ is used in the primary aberration correction phase. This term is particularly important because it represents **aberration propagation** within the system. A larger $\sigma_{Seidel}^2$ implies that small perturbations may result in larger wavefront degradation because some canceled wavefront error can appear with perturbation. Thus, minimizing $\sigma_{Seidel}^2$ during early optimization, it reduces the system’s sensitivity to manufacturing errors.

$$W_{LA}=W_{12101}(\vec{i}\cdot\vec{\rho})(\vec{H}\cdot\vec{\rho}) $$
$$W_{UC}=W_{03001}(\vec{i}\cdot\vec{\rho})(\vec{\rho}\cdot\vec{\rho}) $$
$$LA = \sqrt{\sum_{s=1}^{k}\Delta W_{LAs}^2}$$
$$UC = \sqrt{\sum_{s=1}^{k}\Delta W_{UCs}^2}$$
$$\sigma_W^2 \simeq \frac{W_{040}^2}{180}+\frac{W_{131}^2}{72}+\frac{W_{222}^2}{24}+(W_{220}+\frac{W_{222}}{2})^2$$
$$\sigma_{Seidel}^2 \simeq \sum {W_{040s}}^2 + \sum {W_{131s}}^2 + \sum {W_{222s}}^2 + \sum {W_{220s}}^2$$

## Objective lens design (NA 0.45, f=1mm, FOV=+-1mm)

### Design requirement
![desensitization-and-tolerancing-2.png](../images/desensitization-and-tolerancing-2.png)

Many classical objective lenses are based on the Petzval portrait architecture, which provides a compact solution for moderate f-numbers with a limited number of elements.  
  
To achieve high NA and improved field performance:  
- Rear meniscus elements were introduced for field curvature control  
- A front amici-like configuration was used for high NA  
- The front element was split to increase degrees of freedom  
  

### Tolerancing condition


A simplified tolerance model was used to isolate the effect of aberration-based desensitization.

![desensitization-and-tolerancing-7.png](../images/desensitization-and-tolerancing-7.png)

- 3 Newton rings at 633 nm Radius
- +/- 0.0125 mm thickness
- +/- 0.01 degrees tilt
- Monte Carlo simulation with 1000 trials
- Focus compensator enabled
- Decenter is not used since it can be decomposed as two surface tilts and a thickness change for the systems only with spherical surfaces. 


## Result
Two objective lenses designed are presented below, optimized with or without the desensitization parameters. Both showed fairly good 4th-order aberration correction considering SPHA, COMA, and ASTI values. However, the desensitization case has additional parameters with low weight, including the **sensitivities of UC and LA** (rows 36 and 38), the **wavefront error estimate** (row 46), and the **wavefront propagation** (row 48). This does supportive aberration correction for plane symmetry sensitivity and overall wavefront error. It is a good example showing multiple local minima in lens design. Having additional corrections allows the design to start near more preferred points, especially for better as-built performance. 

The RMS wavefront performance is almost comparable: 0.034 for no desensitization and 0.039 for desensitization. Both nominal designs show diffraction-limited system performance.

However, with tolerancing, the performance of the design without desensitization deteriorates significantly, achieving **~34% manufactuing yield** to obtain 0.07 RMS wavefront error. On the other hand, the design with desensitization shows **more than 90% yield**. This result shows that correcting lens design with additional aberration terms, including LA and UC, is highly effective for as-built performance lens design


### 1. Design without desensitization

#### Merit function
![desensitization-and-tolerancing-6.png](../images/desensitization-and-tolerancing-6.png)
![desensitization-and-tolerancing-5.png](../images/desensitization-and-tolerancing-5.png)

#### Nominal performance
![desensitization-and-tolerancing-10.png](../images/desensitization-and-tolerancing-10.png)

#### Tolerancing
![desensitization-and-tolerancing-9.png](../images/desensitization-and-tolerancing-9.png)

### 2. Design with desensitization
#### Merit function
![desensitization-and-tolerancing-3.png](../images/desensitization-and-tolerancing-3.png)
![desensitization-and-tolerancing-4.png](../images/desensitization-and-tolerancing-4.png)
#### Nominal performance
![desensitization-and-tolerancing-11.png](../images/desensitization-and-tolerancing-11.png)

#### Tolerancing
![desensitization-and-tolerancing-8.png](../images/desensitization-and-tolerancing-8.png)


## Reference
- Jose Sasian, Introduction to Lens Design
- Jose Sasian, Introduction to Aberrations in Optical Imaging Systems, Ch 15
- Jose Sasian, "Lens desensitizing: theory and practice," Appl. Opt., 2022
- Leticia Carrión-Higueras,  Arnau Calatayud, and  José M. Sasian"Improving as-built miniature lenses that use many aspheric surface coefficients with two desensitizing techniques," Optical Engineering, 2021
- Jose Sasian, Control of Linear Astigmatism Aberration in a Perturbed Axially Symmetric Optical System and Tolerancing. Appl. Sci. 2021