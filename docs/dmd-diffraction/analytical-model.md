---
title: Analytical Model of DMD Diffraction
layout: default
comments: true
nav_order: 1
parent: Modeling DMD Diffraction in Zemax
---


# Analytical Model of DMD Diffraction

## Definitions

The system is modeled as a 2D reflective blazed grating. The interaction between the incident light and the mirror surface is defined by two governing vector conditions. The system is defined by its periodicity (pitch), mirror geometry, and the tilt angles.

### Geometry Constants

- **Pitch:** $p_x, p_y$
- **Mirror Size:** $a_x, a_y$ (where $a < p$)
- **Tilt Angle:** $\theta_t$
- **Wavelength:** $\lambda$
- **Incident Wavevector:** 
$$\mathbf{k}_{in}=k\mathbf{\hat{k}}_{in}=\frac{2\pi}{\lambda}\mathbf{\hat{k}}_{in}$$

As a representative example, the TI DMD 7000 series features a pitch ($p$) of 13.68 µm. Assuming a fill factor of 0.96, the mirror width ($a$) is approximately 13.13 µm. While standard operation employs a binary state ( $\theta_t = \pm12^\circ$ ), this model treats the tilt as a continuous variable, $12 ^\circ \leqq \theta_t \leqq 12^\circ$ .This allows the simulation to account for intermediate transition states or specialized beam-steering applications where the mirror position is modulated between the nominal binary landing states.

### Fundamental Vector Equations

Two governing equations define the DMD mirrors: (1)  grating equation and (2) reflection equation. Here, all DMD mirrors are assumed to have the same tilt angle.

- **The Grating Equation**
The periodicity of the DMD array $(p_x,p_y)$ dictates the discrete angles where constructive interference occurs. The outgoing wavevector $k_{out}$ must satisfy:

$$\mathbf{k}_{out,\parallel} = \mathbf{k}_{in,\parallel} + \mathbf{G}_{mn}, \quad \text{where } \mathbf{G}_{mn} = \left( \frac{2\pi m}{p_x}, \frac{2\pi n}{p_y} \right)$$
- **The Reflection Equation**
The tilt angle  $θ_t$ determines the norma vectorl $\hat n$ of the mirror. This normal defines the **specular reflection vector** kspec, which serves as the center of the diffraction envelope:

$$\mathbf{k}_{spec} = \mathbf{k}_{in} - 2(\mathbf{k}_{in} \cdot \mathbf{\hat{n}}) \mathbf{\hat{n}}$$

---

## Fraunhofer Diffraction Derivation

The far-field complex amplitude $E(\mathbf{k}_{out})$ is the Fourier Transform of the total aperture function $A(\mathbf{r})$.

$E( \mathbf{k_{out}} )=∫A(r) e^{(i \mathbf{(k_{in}-k_{out} )r}}) dr=∫A(r) e^{(i\mathbf{qr)}} dr,$ 

where $A(r)$ is a complex aperture function, $\mathbf{q}=\mathbf{k_{in}-k_{out}}$ and $E(\mathbf{k_{out}} )=\tilde {A}(\mathbf{q})$.

### Fraunhofer Approximation

A critical consideration in DMD diffraction modeling is the Fraunhofer distance ($L \gg D^2/\lambda)$ . In practice, the effective aperture diameter D is defined by the **incident beam diameter** rather than the physical boundaries of the DMD chip itself.

The distance required to reach the far-field quadratically with the number of mirrors involved in the diffraction. Taking a TI 7000 DMD (13.68 μm pitch) and a wavelength of λ=905 nm as an example:

| **Case**       | **Beam Diameter (D)** | **Number of Mirrors**      | **Fraunhofer Distance ($D^2/λ$)** |
| -------------- | --------------------- | -------------------------- | --------------------------------- |
| **Small Beam** | 1.37 mm               | $\approx 100 \times 100$   | $\approx 20  cm$                  |
| **Large Beam** | 13.7 mm               | $\approx 1000 \times 1000$ | $\approx 2  m$                    |

### The Total Aperture Function

The DMD is modeled as a convolution of a single mirror's profile $P(\mathbf{r})$ with a finite 2D Dirac comb $\Lambda(\mathbf{r})$:

$A(\mathbf{r}) = P(\mathbf{r}) * \sum_{m=0}^{M-1} \sum_{n=0}^{N-1} \delta(\mathbf{r} - \mathbf{R}_{mn})$

where 
$\mathbf{R}_{mn} = (mp_x, np_y).$

### The Lattice Factor

The Fourier Transform of the Dirac comb $\tilde{\Lambda}(\mathbf{q})$ yields the interference term. For $M, N$ mirrors:

$\tilde{\Lambda}(\mathbf{q}) = \sum_{m=0}^{M-1} e^{-i q_x m p_x} \sum_{n=0}^{N-1} e^{-i q_y n p_y}$

Using the geometric series identity, the resulting intensity contribution is:

$\vert\tilde{\Lambda}(\mathbf{q})\vert^2 =  \vert \frac{\sin(M q_x p_x / 2)}{\sin(q_x p_x / 2)} \vert^2 \vert \frac{\sin(N q_y p_y / 2)}{\sin(q_y p_y / 2)} \vert^2$

Assuming that we have enough mirrors ($M,N → \infty$), $\tilde{\Lambda}(\mathbf{q})$ can be approximated to delta comb}

 $\tilde{\Lambda}(\mathbf{q}) = \frac{(2\pi)^2}{p_x p_y} \sum_{m,n} \delta\left(q_x - \frac{2\pi m}{p_x}\right) \delta\left(q_y - \frac{2\pi n}{p_y}\right)$

### The Single Mirror Factor (Diffraction Envelope)

The tilted mirror introduces a phase ramp $\phi(x,y)$. Given the mirror normal $\mathbf{\hat{n}}$for a diagonal tilt:

$$\mathbf{\hat{n}} = \left[ \frac{\sin \theta_t}{\sqrt{2}}, \frac{\sin \theta_t}{\sqrt{2}}, \cos \theta_t \right]$$

The phase gradient results in a shifted sinc envelope. We define the blaze vector $\mathbf{q}_{blaze}$ as the momentum transfer at specular reflection:

$$\mathbf{q}_{blaze} = - 2(\mathbf{k}_{in} \cdot \mathbf{\hat{n}}) \mathbf{\hat{n}}$$

The single mirror transform is:

$$$\tilde{P}(\mathbf{q}) = a_x a_y \text{sinc}\left( \frac{a_x (q_x - q_{blaze,x})}{2} \right) \text{sinc}\left( \frac{a_y (q_y - q_{blaze,y})}{2} \right)$$

### Total Intensity Distribution

The final intensity $I(\mathbf{q})$ is the product of the single-mirror envelope and the lattice factor:

$I(\mathbf{q}) = {\vert\tilde{P}(\mathbf{q})\vert^2} ({\text{Envelope}}) \times {\vert\tilde{\Lambda}(\mathbf{q})\vert^2} ({\text{Lattice Factor}})$

$I(\mathbf{q}) =  a_x a_y \text{sinc}\left( \frac{a_x (q_x - q_{blaze,x})}{2} \right) \text{sinc}\left( \frac{a_y (q_y - q_{blaze,y})}{2} \right) \times \vert \frac{\sin(M q_x p_x / 2)}{\sin(q_x p_x / 2)} \vert^2 \vert \frac{\sin(N q_y p_y / 2)}{\sin(q_y p_y / 2)} \vert^2$
 

 Physical Interpretation

- **The Grid:** The $\sin(N\dots)/\sin(\dots)$ terms create a fixed grid of diffraction spots in angular space, determined solely by the pitch $p$ and $\lambda$.
- **The Brightness:** The $sinc$ terms determined brightness. As the mirror tilts ($\theta_t$ changes), the $sinc$ envelope slides over the grid, illuminating different diffraction orders.

---