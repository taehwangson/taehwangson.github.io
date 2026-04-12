---
title: System setup 
layout: default
comments: true
nav_order: 1
parent: Alignment Study
---

## System setup 
### Nominal design
- The imaging system consists of two off-the-shelf Thorlabs lenses: an objective lens ([TL4x-SAP](https://www.thorlabs.com/item/TL4X-SAP)) and a tube lens ([TTL200](https://www.thorlabs.com/item/TTL200)). Since black-box models are used, the system can be simplified into four primary elements: sample plane, objective lens, tube lens, and camera plane.
![critical-dimension-2.png](../images/critical-dimension-2.png)

- The field of view is limited to ±2 mm to ensure near-diffraction-limited performance under nominal (aligned) conditions. 

Wavefront error across the field
![critical-dimension-13.png](../images/critical-dimension-13.png)
 
- Monochromatic illumination at 532 nm is used.


### Distortion and PSF centroid shift

Distortion is defined as the deviation between the real chief ray intersection and the first-order paraxial image position. In this work, distortion is evaluated by comparing paraxial ray coordinates (PARX/PARY) with real ray intercepts (REAX/REAY).

Because the system loses rotational symmetry under perturbations, distortion is evaluated at 9 field positions, and the average distortion is reported for consistency.
![critical-dimension-5.png](../images/critical-dimension-5.png)
![critical-dimension-6.png](../images/critical-dimension-6.png)While distortion captures geometric image displacement, it does not fully represent the effective image location when wave effects are considered. The diffraction-based PSF can exhibit an additional centroid shift due to asymmetric aberrations.

The Huygens PSF analysis provides the centroid offset relative to the real ray intersection point, capturing wave-optics-induced displacement that is not represented in geometric distortion.

Zernike decomposition helps interpret this behavior. The difference between RMS (to chief ray) and RMS (to centroid) indicates that a significant portion of the wavefront error arises from tilt ($Z_3$), corresponding to image displacement. In addition, non-zero coma terms ($Z_7$) introduce asymmetry in the PSF, resulting in additional centroid bias.

On-axis (Hx=0, Hy=0) PSF and Zernike coefficients
![critical-dimension-10.png](../images/critical-dimension-10.png)
Off-axis (Hx=0, Hy=1) PSF and Zernike coefficients
![critical-dimension-9.png](../images/critical-dimension-9.png)

Therefore, the effective image displacement relevant to metrology can be expressed as:
$$ Total \ image \ shift=distortion \ (geometric)+PSF \ centroid\  shift \ (wave)$$
This distinction is critical for CD metrology, where even small centroid shifts can translate directly into systematic measurement bias.

### Perturbation / Tolerancing
Compared to axial spacing errors, tilt and decenter of optical elements are more detrimental to imaging performance. Once perturbations are introduced, the system no longer maintains rotational symmetry, and performance cannot be fully described using 1D field-dependent plots.

For example, when an element is decentered along the y-axis, the wavefront errors at Hy = +1 and Hy = -1 are no longer identical. To account for this asymmetry, 9 field points are used for evaluation, as described earlier.

Decenter and tilt are implemented using Coordinate Break surfaces. Surface indices 1, 4, 13, and 20 correspond to the sample plane, objective lens, tube lens, and camera plane, respectively.

![critical-dimension.png](../images/critical-dimension.png)

Layouts with (1) no perturbation, (2) tube lens 1 mm decenter, and (3) tube lens 1 deg tilt
![critical-dimension-12.png](../images/critical-dimension-12.png)
