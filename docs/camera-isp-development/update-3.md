---
title: Update 3 - CFA interpolation issue
layout: default
comments: true
nav_order: 3
parent: Camera ISP Development
---

# Update 3 - CFA interpolation issue

Bilinear interpolation, a non-adaptive averaging method, is prone to creating pseudo-color artifacts and zipper noise because it fails to distinguish between smooth areas and sharp edges, leading to unnatural, rapid color transitions.

In contrast, edge-directed demosaicing methods, such as VNG, overcome this limitation. They adaptively suppress these artifacts by first detecting the direction of sharp edges (high gradients) and then interpolating along that edge, thereby preserving detail and color.

![Reference: [Book] Theory and Applications of Smart Cameras, Architectural Analysis of a Baseline ISP Pipeline](../images/image-32.png)

Reference: [Book] Theory and Applications of Smart Cameras, Architectural Analysis of a Baseline ISP Pipeline

(1) Bilinear interpolation

![Exp 15ms, Gain6, [JAI GO-2400C-USB](../images/image-33.png)

Exp 15ms, Gain6, [JAI GO-2400C-USB](https://www.jai.com/products/go-2400c-usb)

![Zoom-in image (left bottom)](../images/image-34.png)

Zoom-in image (left bottom)

(2) Variable Number of Gradients (VNG) interpolation

![image.png](../images/image-35.png)

![image.png](../images/image-36.png)

(3) Reference image taken by Galaxy A8 star

![image.png](../images/image-37.png)

![image.png](../images/image-38.png)

Although VNG interpolation removes color noise where color changes rapidly, it does not remove chromatic change from single-pixel bright spots. This is due to color aliasing, where the bright dot is composed of a few pixels and causes sampling issues under the Bayer pattern. DDFPD uses the local chromatic components (R−G and B−G differences) for both the horizontally and vertically interpolated results. It selects the result that shows greater local color smoothness/homogeneity. However, the example below shows that some degree of false color light spots, even after DDFPD interpolation. Further correction will be discussed later with chroma filtering.

(1) Variable Number of Gradients (VNG) interpolation

![Exp 15ms, Gain6, [JAI GO-2400C-USB](../images/image-35.png)

Exp 15ms, Gain6, [JAI GO-2400C-USB](https://www.jai.com/products/go-2400c-usb)

![Zoom-in image (left bottom)](../images/image-39.png)

Zoom-in image (left bottom)

(2) DDFPD (Demosaicing With Directional Filtering and *a posteriori* Decision) - [D. Menon 2007](https://ieeexplore.ieee.org/document/4032820)

![image.png](../images/image-40.png)

![image.png](../images/image-41.png)

(3) Reference image taken by Galaxy A8 star

![image.png](../images/image-37.png)

![image.png](../images/image-42.png)

---