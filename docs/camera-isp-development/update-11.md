---
title: Update 11 - Fixed pattern noise removal
layout: default
comments: true
nav_order: 11
parent: Camera ISP Development
---

# Update 11 - Fixed pattern noise removal

**1. Introduction to Sensor Noise**

Noise in CMOS image sensors is broadly categorized into **temporal** and **spatial** components. Temporal noise includes shot noise, readout noise, and thermal noise, manifesting as signal fluctuations over time. These characteristics are typically evaluated using the Photon Transfer Curve (PTC) method, as outlined in the **EMVA 1288** guidelines [Photon transfer, J. R. Janesick, EMVA Standard 1288].

In contrast, spatial noise—commonly referred to as **Fixed Pattern Noise (FPN)**—represents the pixel-to-pixel intensity variations that persist after temporal noise is removed via frame averaging. Unlike CCDs, CMOS sensors exhibit significant FPN due to manufacturing variances in the individual transistors integrated into each pixel. While EMVA 1288 utilizes **DSNU** (Dark Signal Non-Uniformity) and **PRNU** (Photo Response Non-Uniformity) as global performance metrics, these single-value statistics do not fully characterize the pixel-level dependency on camera gain and exposure time. **PRNU was tested first but it did not produce speckle like spatial noise. Therefore, removing DSNU noise is major target.** 

**2. Proposed FPN Model**

By analyzing the mean intensity of 100-frame averaged dark images across varying gains (G) and exposure times (t), we observed a high degree of linearity (R² = 0.97). This suggests that the intensity of a pixel at coordinates (i,j) can be modeled linearly:

$$I_{FPN}(i,j)=G*[S(i,j)+K(i,j)*t+O_{input}(i,j)]+O_{output}(i,j)$$

where: S(i,j): The photon-induced signal, defined as:

$$S(i,j)=\frac{A*E(i,j)*t*QE*G_{sys}}{hc/\lambda}$$ 

note that A, E, QE, and $G_{sys}$ are sensor area, irradiance, quantum field, and system conversion factor.

K(i,j): The pixel-specific dark current generation rate.

$O_{input}(i,j)$: Fixed pattern offset generated before amplification

$O_{output}(i,j)$: Black level, used to prevent signal clipping during Analog-to-Digital Conversion (ADC).

![mean pixel value (ADU) of 100-frame average dark images w.r.t. gain and exposure time](../images/image-116.png)

mean pixel value (ADU) of 100-frame average dark images w.r.t. gain and exposure time

Images below are 100x100 pixels cropped images showing linear intensity increment with respect to gain and exposure time. As we saw in mean pixel value relation, the overall intensity is increased as gain and exposure time increase. But speckle patterns are major correction targets, which are potentially caused by manufacting errors.

![100x100 pixel cropped original dark image with different gains and exposure times ](../images/image-117.png)

100x100 pixel cropped original dark image with different gains and exposure times 

**3. Calibration method**

To isolate the model parameters, a two-step calibration was performed.

**3.1. Fixed Pattern Offset, $O_{input}(i,j)$**

First, images were captured at a minimum exposure time (t=10μs) to suppress the contributions of S(i,j) and K(i,j). In this regime, the model simplifies to a linear function of gain:

$$ I_{FPN}(i,j)≈G⋅O_{input}(i,j)+O_{output}(i,j) $$

Plotting intensity versus gain allowed us to extract $O_{input}(i,j)$ as the slope of the linear fit. While most pixels remained near the baseline black level, specific "hot pixels" exhibited significant slopes, identifying them as high-offset outliers.

![Calibration for $O_{input}(i,j)$. The graph shows $I_{FPN}(i,j)=G(i,j)*O_{input}(i,j)+O_{output}(i,j)$ relation because t is only 10us. The slope of linear fit line is $O_{input}(i,j)$](../images/image-118.png)

Calibration for $O_{input}(i,j)$. The graph shows $I_{FPN}(i,j)=G(i,j)*O_{input}(i,j)+O_{output}(i,j)$ relation because t is only 10us. The slope of linear fit line is $O_{input}(i,j)$

**3.2. Dark Current Rate, K(i,j)** 

With $O_{input}$ characterized, the dark current rate was isolated by varying the exposure time at a fixed gain.

$I_{FPN}(i,j)=(G⋅K)t+O_{output}(i,j)$

The slope of the intensity versus exposure time plot yields the dark current rate K(i,j). This parameter is critical for night vision applications, where long integration times amplify thermal leakage.

![](../images/image-119.png)

Calibration for k(i,j). The left graph shows $I_{FPN}(i,j)=G(i,j) \cdot [K(i,j) \cdot t]+O_{output}(i,j)$ relation because $O_{output}(i,j)$  is corrected. The slope of linear fit line is K(i,j).

**4. Result and discussion**

In standard operating conditions, spatial noise is often masked by temporal readout noise. However, in high-gain night vision applications, FPN frequently exceeds the temporal noise floor ($3σ_{temporal}$), appearing as false signals or "speckle." The effectiveness of the proposed correction is demonstrated through histogram analysis. Note that the histogram below shows pixel value distribution from original dark images at gain 8, $O_{input}(i,j)$ corrected images, and $O_{input}(i,j)$ and k(i,j) corrected images and the vertical lines correspond to $3σ_{temporal}$. The effective noise removal can be confirmed with dark images after $O_{input}(i,j)$ and k(i,j) corrected images. The raw dark signal distribution is significantly improved after correcting for $O_{input}$ and K(i,j). Notably, a small population of "impulse noise" remains; these are attributed to pixels reaching the hardware saturation limit, where the linear model no longer applies.

![image.png](../images/image-120.png)

![100x100 pixel cropped images after $O_{input}(i,j)$ correction](../images/image-121.png)

100x100 pixel cropped images after $O_{input}(i,j)$ correction

![100x100 pixel cropped images after $O_{input}(i,j)$ and k(i,j) corrected images](../images/image-122.png)

100x100 pixel cropped images after $O_{input}(i,j)$ and k(i,j) corrected images

Finally, the correction was applied to a representative night vision outdoor scene. The removal of FPN eliminated noticeable impulse noise that would otherwise trigger false detections in chroma filters or computer vision algorithms.

![night vision image (gain 6, exposure time 15ms)](../images/image-123.png)

night vision image (gain 6, exposure time 15ms)

---