---
title: Update 2 - LSC overcorrection
layout: default
comments: true
nav_order: 2
parent: Camera ISP development
---

## Update 2 - LSC overcorrection

Initial testing of the indoor scene revealed excessive brightness at the image periphery, indicating an overcorrection in the Lens Shading Correction (LSC) pipeline. The original LSC calibration map was derived using a captured image of a white monitor display. However, this yielded an extreme maximum gain factor exceeding 8.0 at the corners, implying that peripheral objects possessed only 12.5% of the intensity of central objects. Even accounting for aggressive optical vignetting, a gain of 8.0 is improbable for this optical design.

The discrepancy was traced to the non-Lambertian emission profile of the monitor. Most LCD displays exhibit strong directionality. When viewed at the steep angles associated with the lens's peripheral field, the perceived intensity drops sharply compared to the normal (center) view. To rectify this, a new calibration dataset was acquired using a white wall under indirect, uniform room lighting. To mitigate non-uniformities in the wall surface, multiple frames were averaged. This averaged dataset was then fitted to a 2D polynomial function to generate a smooth, mathematically robust estimate of the system’s relative illumination.

The resulting gain factor from the wall-based calibration was approximately 1.85, a significantly more realistic value for this lens-sensor combination. Both indoor and outdoor test cases confirm that the monitor-based calibration induced unnatural bright artifacts, whereas the wall-based polynomial fit provided a natural illumination balance.

This highlights the necessity of using a Lambertian source for experimental LSC calibration. Relying solely on optical simulation can be insufficient, as final relative illumination is a composite of multiple factors: mechanical/optical vignetting, the radiometric cos4(θ) law, and CMOS sensor roll-off (pixel vignetting caused by Chief Ray Angle mismatch) [[Edmund optics ref](https://www.edmundoptics.com/knowledge-center/application-notes/imaging/sensor-relative-illumination-roll-off-and-vignetting/?srsltid=AfmBOoqElK6QWmWLCYhiStaRj9mkJ2JazP_-Ci17PWzCAOg7krYwvIgl](https://www.edmundoptics.com/knowledge-center/application-notes/imaging/sensor-relative-illumination-roll-off-and-vignetting/?srsltid=AfmBOoqElK6QWmWLCYhiStaRj9mkJ2JazP_-Ci17PWzCAOg7krYwvIgl))].

**(1) Image of a painted wall with indirect lighting for calibration** 

**(Left: calibration with monitor display, right: calibration with wall)**

![image.png](../images/image-26.png)

![image.png](../images/image-27.png)

**(2) Indoor lighting**

**(Left: calibration with monitor display, right: calibration with wall)**

![image.png](../images/image-28.png)

![image.png](../images/image-29.png)

**(3) Outdoor daylight image** 

**(Left: calibration with monitor display, right: calibration with wall)**

![image.png](../images/image-30.png)

![image.png](../images/image-31.png)

---