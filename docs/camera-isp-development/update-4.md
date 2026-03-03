---
title: Update 4 - Adaptive AWB Gain Tuning, Weighted Gray World Compensation
layout: default
comments: true
nav_order: 4
parent: Camera ISP development
---

# Update 4 - Adaptive AWB Gain Tuning, Weighted Gray World Compensation

The overall color reproduction was evaluated after AWB and CCM using indoor and outdoor images. The indoor lighting was set to 2700 K (warm white). Initial testing showed the proposed ISP neutralized the ambient warmth, producing a clinical white result. This suggests the Gray World Hypothesis (GWH) was over-correcting the illuminant. To preserve the perceptual 'warmth' of the indoor setting, a weighting factor ($w_{gwh}$) was introduced. A weight of 0.5 produced a color response similar to the iPhone 16, whereas values closer to 0 (preserving more of the ambient color) produced a warmer color similar to the Galaxy A8 Star. This tuning shows that AWB lies at the balance between color accuracy and perceptual adaptation.

$Gain_G=1$

$Gain_{R,adjusted}= 1+(Gain_R -1)*w_{gwh}$   

$Gain_{B,adjusted}= 1+(Gain_B -1)*w_{gwh}$   

![Ref: [egansign.com](images/image-43.png)

Ref: [egansign.com](http://egansign.com/)

On the other hand, outdoor daylight images demonstrate superior color fidelity when the Gray World Hypothesis (GWH) is disabled ($w_{gwh}=0$). Since sunlight serves as the standard illuminant (D65), its chromaticity is a known constant; applying GWH introduces a risk of 'subject failure.' For example, in scenes with a dominant blue sky, GWH erroneously suppresses the blue channel to achieve a neutral average, resulting in an unnatural yellow-cyan tint. While a fixed weight of 0.5 provides a baseline compromise for mixed lighting, a more robust approach involves adaptively modulating the weight based on exposure time or sensor Gain, effectively transitioning from fixed daylight presets to active AWB correction as the scene shifts from outdoor to indoor environments

Gray world hypothesis weight $w_{gwh}$ =1.0

![image.png](images/image-44.png)

![image.png](images/image-45.png)

Gray world hypothesis weight $w_{gwh}$ =0.5

![image.png](images/image-46.png)

![image.png](images/image-47.png)

Gray world hypothesis weight $w_{gwh}$ =0

![image.png](images/image-48.png)

![image.png](images/image-49.png)

IPhone 16

![IMG_1499.jpg](IMG_1499.jpg)

![image.png](images/image-50.png)

![iPhone camera’s ISP changes AWB depending on scene and screen touch point ](ezgif-36f2f76e5e27a4ba.gif)

iPhone camera’s ISP changes AWB depending on scene and screen touch point 

Galaxy A8 star

![13-1. GalaxyA8_20251215_165452.jpg](13-1._GalaxyA8_20251215_165452.jpg)

![image.png](images/image-51.png)

---