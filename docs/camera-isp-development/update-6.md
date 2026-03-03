---
title: Update 6 - Color correction matrix (CCM) revisit
layout: default
comments: true
nav_order: 6
parent: Camera ISP development
---


# Update 6 - Color correction matrix (CCM) revisit

The factory white balance corrects for channel-dependent sensitivity variations in the sensor using a neutral gray target. Subsequently, the Gray World Hypothesis (GWH) is often employed in Auto White Balance (AWB) algorithms to shift the image’s average chromaticity toward the white point. The Color Correction Matrix (CCM) is typically applied after these white balance stages to improve color accuracy.

For a robust ISP pipeline, the CCM must adhere to two critical constraints regarding its row sums:

1. Neutral Preservation: The CCM must not shift the white point established in the previous step. This is ensured when the row sums are identical across the R, G, and B channels. If the row sums are unequal, a neutral gray input (R=G=B) will emerge from the matrix with a color tint, resulting in an unnatural appearance.
2. Luminance Conservation: Each row sum should be exactly 1.0. If the sums deviate from unity, the application of the CCM will modify the overall Luma of the image, causing it to appear brighter or dimmer. As shown in the example below, the image corrected with a CCM that does not conserve luminance cannot fully use the 8-bit dynamic range.

In Python, these constraints (∑Row=1) can be strictly enforced by using the `scipy.optimize.minimize` function with a linear equality constraint, ensuring that the mathematical fit for color does not compromise the physical neutrality of the image.

1. **Indoor image before CCM**

![image.png](images/image-61.png)

1. **Indoor image after CCM (∑Row≠1)**

![image.png](images/image-62.png)

![image.png](images/image-63.png)

1. **Indoor image after CCM (∑Row=1)**

![image.png](images/image-64.png)

![image.png](images/image-65.png)

---