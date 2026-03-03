---
title: Update 1-Green color on peripheral pixels
layout: default
comments: true
nav_order: 1
parent: Camera ISP development
---


## Update  1 - Green color on peripheral pixels

**1. Problem statement**

After color correction, the peripheral pixel color is shifted to green. Especially the darker area is noticeably greenish. It is more obvious after CCM. CCM matrix shifts the green-ish peripheral more green tone.

![image.png](images/image-20.png)

---

**2. Test with the color checker image**

The same ISP pipeline was used for the 24 colorchecker target. The CCM application makes color patches more vivid, but it causes a greenish image on gray color pixels.

- 24 Colorcheck image CCM application

![image.png](images/image-21.png)

---

**3. Test with LSC calibration image**

The LSC calibration image was used as input to the ISP pipeline to detect the color shift. First of all, there is a mild green and red shift in the grey points. This may be attributed to overcorrected CCM. The other factor making this shift worse is LSC itself. The peripheral area boosted by the LSC gain looks much worse than the center. Even before the CCM correction, the LSC corrected image shows more green in the darker pixels.

- Gray image without LSC

![image.png](images/image-22.png)

- Gray image with LSC

![image.png](images/image-23.png)

---

**Result** 

It turns out the LSC calibration map was generated without the BLC step. Therefore, a non-uniform intensity distribution was observed even in the calibration image. However, it is observed some green shift after CCM. It is highly likely related to the neutral preservation issue of the CCM matrix, which will be covered in the later update.

- Gray image with updated LSC

![image.png](images/image-24.png)

![image.png](images/image-25.png)
