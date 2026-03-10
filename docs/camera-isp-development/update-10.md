---
title: Update 10- Chroma filtering
layout: default
comments: true
nav_order: 10
parent: Camera ISP Development
---

# Update 10- Chroma filtering

While the Luminance (Y) channel path targets stochastic grain and edge enhancement, the Chroma (Cr, Cb) channels undergo edge-based filtering to suppress chroma haziness and eliminate speckle-like false color artifacts.

Initially, a 3×3 median filter is applied to the Chroma channels to remove impulse noise. Subsequently, the pixel values are modulated according to the following relationship:

$$C_{out,r} = 0.5+(C_{in,r}-0.5)\times(1-M({\mid}E{\mid}))$$

$$C_{out,b} = 0.5+(C_{in,b}-0.5)\times(1-M({\mid}E{\mid}))$$

![image.png](../images/image-102.png)

Where $ M({\mid}E{\mid})$ represents the suppression Mask derived from the Luma edge map. Under high-gradient conditions (strong edges), the term $(1-M({\mid}E{\mid}))$ approaches 0, effectively desaturating the pixel to neutral gray. Conversely, in low-gradient regions (weak edges), the term approaches 1, preserving the original chromatic integrity.

In an outdoor night scene, false color spots on vehicle surfaces are effectively neutralized using thresholds of `threshold_low=0.1, threshold_high=0.3` .

However, applying these same parameters to an outdoor daylight scene resulted in overcorrection. This was visible as unnatural "white halos" or desaturated edges on high-contrast objects, such as yellow signs and utility poles. By relaxing the thresholds to `threshold_low=0.1, threshold_high=0.6`, the edges successfully retained their original color. This demonstrates that the suppression thresholds must be adaptively tuned based on exposure time or analog gain to prevent peripheral desaturation in high-signal environments.

**Outdoor night scene**

![Correction for false color spot on vehicle surfaces ](../images/image-109.png)

Correction for false color spot on vehicle surfaces 

![Before edge-based chroma filtering](../images/image-110.png)

Before edge-based chroma filtering

**Outdoor day scene**

![After edge-based chroma filtering](../images/image-111.png)

After edge-based chroma filtering

![white halos near yellow sign and utility pole](../images/image-112.png)

white halos near yellow sign and utility pole

![image.png](../images/image-113.png)

![Before edge-based chroma filtering](../images/image-114.png)

Before edge-based chroma filtering

![After edge-based chroma filtering](../images/image-115.png)

After edge-based chroma filtering

---