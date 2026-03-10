---
title: Update 5 -  Sensor linearization and white balance calibration based on color checker board
layout: default
comments: true
nav_order: 5
parent: Camera ISP development
---


# Update 5 -  Sensor linearization and white balance calibration based on color checker board

The 24-patch color checker is an excellent tool for evaluating sensor linearity. The relative luminance (Y) values of the grayscale patches (19–24) are known standards. Ideally, these reference values should be directly proportional to the experimental raw RGB intensities.

The observed non-linearity in this setup likely stems from two factors: the camera not being a "scientific grade" linear sensor, and the use of a display-based target rather than a physical chart. A true sensor linearity assessment should be conducted using a pigment-based target to avoid display-gamma and flare interference. For this study, however, we assume the non-linearity is a characteristic of the imaging system.

**1. Independent RGB Non-linear Fitting**

Initially, the R, G, and B channels were independently fitted to the reference Y values. While this method yielded the best color accuracy for the color checker patches (by simultaneously correcting luminance and factory white balance), it failed critically in real-world scenarios. In the indoor lighting image, saturated pixels near the lamp exhibited a "fire-like" color gradient. This is a chromaticity shift caused by the independent non-linear modulation of RGB channels, which shears the color ratios as they approach the clipping point. Even with Gray World Hypothesis (GWH) applied, the highlights failed to stay neutral.

**2. G-Channel Linearization with Scalar Gains**

Next, we attempted to use only the Green channel for luminance linearization, applying the resulting curve to all channels, followed by scalar R and B gains for white balance. Surprisingly, this also resulted in a yellow-green color gradient near the lamp highlights. This suggests that applying any non-linear "straightening" curve to data that is near saturation can distort the ratios if the channels do not clip at the same physical intensity.

**3. Linear Fitting**

Ultimately, Linear Fitting (Scalar Gains) produced the most robust results for the indoor lighting scene. It eliminated the artificial color gradients at the saturated boundaries. This demonstrates that linear-gain-based white balance is practically superior to non-linear fitting for maintaining highlight integrity. It is critical to ensure the y-intercept of the fitting curve is near zero. Any residual offset is amplified during white balance and can cause a fatal failure in the GWH algorithm, leading to significant color casts in the shadows.

**1. Independent RGB Non-linear Fitting**

![image.png](../images/image-52.png)

![24 colorchecker](../images/image-53.png)

24 colorchecker

![GWH applied ](../images/image-54.png)

GWH applied 

**2. G-Channel Linearization with Scalar Gains**

![image.png](../images/image-55.png)

![image.png](../images/image-56.png)

![image.png](../images/image-57.png)

**3. Linear fitting ( y intercept = 0 )**

![image.png](../images/image-58.png)

![image.png](../images/image-59.png)

![image.png](../images/image-60.png)

---