---
title: Update 8 - Anti-aliasing (AA) filter
layout: default
comments: true
nav_order: 8
parent: Camera ISP development
---

# Update 8 - Anti-aliasing (AA) filter

While a bilateral filter is an effective option for noise reduction, it carries the inherent risk of identifying the aliasing artifacts as important edges and protecting them from being smoothed. In the absence of a physical Optical Low-Pass Filter (OLPF), the imaging system requires a digital alternative to mitigate aliasing artifacts, such as Moiré patterns.

To evaluate this, a synthetic MTF-style line grating was generated using HTML to simulate high spatial frequencies. The test pattern was matched to the sensor's native resolution (1936x1216) with pitches varying from 2 to 64 pixels. According to the Nyquist sampling theorem, the maximum resolvable frequency is half the sampling rate; however, due to the Bayer Color Filter Array (CFA), the effective Nyquist limit for individual color channels results in a minimum measurable pitch of 4 pixels.

Significant Moiré patterns were observed on the 4px gratings. This is attributed to registration issues. Because the monitor's line gratings were not perfectly aligned with the sensor's pixel grid, the white lines were sampled inconsistently across the RGB sub-pixels, leading to periodic intensity fluctuations and false color.

A 3x3 Binomial (1-2-1) filter successfully eliminated the Moiré patterns. Its frequency response reaches zero magnitude at the Nyquist frequency, providing maximum suppression of aliasing. However, the trade-off is a significant loss of high-frequency detail. The 4px grating was no longer resolvable.

In contrast, (1-4-1) and (1-6-1) filters utilize higher center-pixel weighting to preserve high-frequency information more effectively. While this introduces a trade-off—allowing some residual Moiré to remain—the result is significantly cleaner than the unfiltered raw image. These remaining artifacts can be further alleviated by applying a chroma-specific filter following YUV conversion.

**Line grating image without AA filter**

![image.png](images/image-87.png)

![image.png](images/image-88.png)

**Line grating image with AA filter (1-2-1)**

![image.png](images/image-89.png)

![image.png](images/image-90.png)

![image.png](images/image-91.png)

**Line grating image with AA filter (1-4-1)**

![image.png](images/image-92.png)

![image.png](images/image-93.png)

![image.png](images/image-94.png)

**Line grating image with AA filter (1-6-1)**

![image.png](images/image-95.png)

![image.png](images/image-96.png)

![image.png](images/image-97.png)

---
