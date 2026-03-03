---
title: Update 7 - Lateral chromatic aberration correction
layout: default
comments: true
nav_order: 7
parent: Camera ISP development
---


# Update 7 - Lateral chromatic aberration correction

**Optical Simulation**

Although the specific lens used in this ISP development is unknown, its wide field of view (FOV) suggests a reverse-telephoto or fisheye lens. To estimate the potential magnitude of lateral color, a Zemax reverse-telephoto reference design was re-optimized for a focal length of f=6mm. The design serves as a representative model (field angle ±31∘, F-number 3.5), though it is not fully optimized for production.

The lateral color plots illustrate the deviation of red and blue rays relative to the green reference as the field angle increases. Blue rays exhibit more severe Lateral chromatic aberration (LCA) than red, with a maximum lateral displacement of 2.72μm at the edge of the field. Furthermore, the magnitude of LCA is sensitive to object distance: at a 250mm object position, the LCA increases to 2.97μm, a 0.25μm increase compared to the infinite focus position. However, it is important to note that the LCA measurement itself has a standard deviation of 0.87 μm. LCA measurement will be discussed further in the later part.

**Reverse telephoto F/3.5 f=6mm, FOV +- 31 deg**

![image.png](images/image-66.png)

![Lateral color plot, object at infinity, D 2.72 um at 31 deg field ( 3.62 mm image)](images/image-67.png)

Lateral color plot, object at infinity, D 2.72 um at 31 deg field ( 3.62 mm image)

![Lateral color plot, object at 250mm, D 2.97 um at 31 deg field (3.6w mm image)](images/image-68.png)

Lateral color plot, object at 250mm, D 2.97 um at 31 deg field (3.6w mm image)

![Marginal and chief rays](images/image-69.png)

Marginal and chief rays

![Chief rays for red, green, blue rays at image plane](images/image-70.png)

Chief rays for red, green, blue rays at image plane

**LCA Measurement and Correction**  

LCA was corrected using independent channel warping and non-linear resampling, following the methodology proposed by [[T.E Boult et al.](https://ieeexplore.ieee.org/document/223201)] To quantify the aberration, a dot-grid target was captured. In the presence of LCA, the centroids of the white dots shift across the RGB channels, manifesting as visible color fringes at high-contrast edges.

Centroids were calculated for each dot across all three channels. The resulting 2D vector plots show the displacement of the red and blue channels relative to the green reference. The red channel shift is negligible compared to the blue and lacks axial symmetry, suggesting that the red-channel behavior is likely dominated by manufacturing tolerances or sensor alignment rather than the nominal optical design. The measurement precision was validated by analyzing the circumferential vector components, which are theoretically zero in a rotationally symmetric system. The standard deviation of this circumferential component was 0.87μm ((=0.15 pixels x 5.85 μm/pixel).

Consequently, the red channel shift remains uncorrected in the current iteration, although correction may be feasible if a specific theoretical design model becomes available. In contrast, the blue channel exhibits a clear, monotonic radial shift consistent with the Zemax simulation. This was modeled using polynomial fitting. During the dewarping stage, each pixel coordinate is transformed according to this fit and remapped using sub-pixel interpolation.

![Dot grid pattern ](images/image-71.png)

Dot grid pattern 

![image.png](images/image-72.png)

![Red channel center coordiante shift](images/image-73.png)

Red channel center coordiante shift

![Blue channel center coordiante shift](images/image-74.png)

Blue channel center coordiante shift

![Blue channel, radial vector vs. distance from center](images/image-75.png)

Blue channel, radial vector vs. distance from center

![Blue channel, circumferential vector vs. distance from center](images/image-76.png)

Blue channel, circumferential vector vs. distance from center

![Red channel, radial vector vs. distance from center](images/image-77.png)

Red channel, radial vector vs. distance from center

![Red channel, circumferential vector vs. distance from center](images/image-78.png)

Red channel, circumferential vector vs. distance from center

The comparison images demonstrate a significant improvement in image quality following LCA correction. Prior to correction, the edges of bright regions exhibited distinct green and blue fringing. By shifting the blue channel toward the dot centers, these artifacts were substantially mitigated. While some residual coma aberration is visible in the dot grid, the overall edge acutance is improved. This enhancement is also observable in outdoor scenes, where the previously apparent blue-green color bleeding on structural edges has been visibly reduced.

 

**Zoom-in dot grid pattern (left: before LCA correction, right: after LCA correction)**

![image.png](images/image-79.png)

![image.png](images/image-80.png)

**Outdoor daylight image  (left: before LCA correction, right: after LCA correction)**

![image.png](images/image-81.png)

![image.png](images/image-82.png)

**Zoom-in outdoor daylight image, right bottom  (left: before LCA correction, right: after LCA correction)**

![image.png](images/image-83.png)

![image.png](images/image-84.png)

**Zoom-in outdoor daylight image, left top (left: before LCA correction, right: after LCA correction)**

![image.png](images/image-85.png)

![image.png](images/image-86.png)

---
