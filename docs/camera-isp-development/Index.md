---
title: Camera ISP development
layout: default
comments: true
nav_order: 5
has_children: true
---

# Camera ISP development
<!-- {: .no_toc } -->
Updated on Jan. 20, 2025, Taehwang Son


<!-- ## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc} -->

## Camera spec

![Camera used in the test](images/image.png)
Camera used in the test


Sensor: [JAI GO-2400C-USB](https://www.jai.com/products/go-2400c-usb) Sony IMX174,  2.3 megapixels (1920x1200), pixel 5.86μm
Lens: [LM6HC](https://www.kowa-lenses.com/LM6HC-1-6mm-5MP-C-Mount-Lens/10904?srsltid=AfmBOor15VPXkPNz9Smi4Cd0WQA2JmWWfSadqVhDm4c0PdeqIAd5KcbP) ( 1" f= 6mm 5MP C-Mount Lens ) F/1.8 
Max angular FOV +-54 deg. With the sensor, Angular FOV = +- 48 deg, Wide angle, but with some distortion.

---

## Calibration data acquisition and analysis

### 1. Black level compensation

Black level was acquired by capturing an image with the sensor capped (no lens) using a short exposure time (100μs). Average pixel intensity was calculated to determine the black level. The black level is 8DN.

![Black level test data](images/image-1.png)

Black level test data

---

### 2. Lens shading correction (LSC)

The lens shading correction calibration image was captured using a white monitor screen to simulate uniform illumination. For each color channel (R, G1, G2, B), the image was Gaussian-blurred and normalized so that maximum values remained unchanged by LSC. After applying LSC, the image became flat. The gain map is not symmetrical. This could be caused by the camera optical axis not being well aligned with the monitor. 

![image.png](images/image-2.png)

![Lens shading correction calibration](images/image-3.png)

Lens shading correction calibration

---

### 3. Calibration White Balance

Using the image obtained after LSC, white balance was calibrated by applying gains to the blue and red channels to match the average intensity of the green channel. After applying the gains, the greenish raw image became a gray image.

![Calibration white balance data](images/image-4.png)

Calibration white balance data

---

### 4. Color correction matrix (CCM)

To calibrate color, a 24 colorchecker was displayed on an sRGB monitor and imaged with the camera. The [24 colorchecker](https://github.com/taehwangson/ColorChecker-App) was developed previously for monitor color measurement.

![Screenshot of 24 colorchecker ](images/image-5.png)

Screenshot of 24 colorchecker 

 It is known that the RGB to RGB transformation is done by a 3x3 matrix. The matrix M was calculated using pseudo-inverse ([np.linalg.pinv](https://numpy.org/doc/2.3/reference/generated/numpy.linalg.pinv.html)).

![reference: [Book] Digital Color Management, Appendix H](images/image-6.png)

reference: [Book] Digital Color Management, Appendix H

After the Color Correction Matrix (CCM) application, the 24 ColorChecker's colors looked realistic and close to the screenshot. RGB data were transformed to the u′v′ color space to assess the calibration quality. In terms of the summation of the distance between the reference points and the measured points, the CCM-applied image showed more than twice the performance improvement

![image.png](images/image-7.png)

![image.png](images/image-8.png)

![image.png](images/image-9.png)

---

## ISP pipeline prototype

The full ISP comprises multiple steps. This prototype section covers only the essential processes required to transform raw images into human-visible quality images. In the later sections, additional steps will be added, or existing steps will be modified for better performance.

![Reference: [Book] Theory and Applications of Smart Cameras, Architectural Analysis of a Baseline ISP Pipeline](images/image-10.png)

Reference: [Book] Theory and Applications of Smart Cameras, Architectural Analysis of a Baseline ISP Pipeline

### 1. BLC, LSC, AWB application

![image.png](images/image-11.png)

---

### 2. Noise filter (Bilateral filter)

To remove shot noise or fixed pattern noise, a bilateral filter was applied. A bilateral filter is known as a non-linear filter that reduces noise while sharply preserving edges and fine details

![image.png](images/image-12.png)

---

### 3. Auto white balance (AEB) - Gray world assumption

To implement a simple auto white balance step, the gray world assumption was used. Even after white balance calibration, the image shows that the red and blue channels are weaker than the green channel. Under the gray-world assumption, the red and blue channel gains were adjusted. The edge of the monitor screen looks closer to white after AWB.

![image.png](images/image-13.png)

---

### 4. Color Filter array (CFA) Interpolation - Bilinear interpolation

After AWB, the raw data was finally converted to RGB using the OpenCV module. Before this step, every color image presented above was just for presentation, and the files were in raw format. Here, the simple bilinear interpolation was applied for RGB conversion 

![image.png](images/image-14.png)

---

### 5. Gamma correction

After normalizing linear RGB sensor data, each channel was encoded using the ITU-R BT.709 Opto-Electronic Transfer Function (OETF). This process is necessary to convert linear light into a perceptually uniform non-linear signal. This encoding ensures that the available bit depth is allocated primarily to the darker tones, which the human vision is more sensitive to. The display device then applies its corresponding inverse function, the Electro-Optical Transfer Function (EOTF), to correctly map the received gamma-corrected input back to linear light output.

![image.png](images/image-15.png)

![image.png](images/image-16.png)

---

### 6. Color Correction (CCM)

The gamma-corrected image was transformed using the calibrated CCM matrix.

![image.png](images/image-17.png)

![Colorchecker 24 image (iPhone16)](images/image-18.png)

Colorchecker 24 image (iPhone16)

---

### 7. Color space conversion (Y’CbCr)

RGB images are converted into Y′CbCr because human eyes see brightness and color differently. We notice every tiny detail in brightness (Y′), but we are less sensitive to fine details in color (CbCr). It enables us to remove noise more tactically. Eventually, it prioritizes image sharpness and noise reduction based on the strengths and weaknesses of human vision.

![image.png](images/image-19.png)

---


# Reference

[https://github.com/cruxopen/openISP](https://github.com/cruxopen/openISP)

[https://ieeexplore.ieee.org/document/223201](https://ieeexplore.ieee.org/document/223201)

[Book] Digital Color Management

[Book] Theory and Applications of Smart Cameras, Architectural Analysis of a Baseline ISP Pipeline

[Book] Photon transfer, J. R. Janesick

EMVA Standard 1288,  Standard for Characterization of Image Sensors and Cameras

# Appendix

Note that the LED monitor-generated calibration image is not ideal since the LED is not D65 and its scattering is not likely Lambertian. Moreover, the color and brightness may not be fully calibrated to follow sRGB, so it may cause unexpected results.

**Color checker generator** 

![ScreenRecording.gif](images/ScreenRecording.gif)

**Dot grid pattern generator** 

![ezgif-185b9182663f33fe.gif](images/ezgif-185b9182663f33fe.gif)

**Line grating target**

[Line_periodic.html](Line_periodic.html)

![image.png](images/image-124.png)

**Noise filter tuner**

![ezgif-1a261b57bfa2beda.gif](images/ezgif-1a261b57bfa2beda.gif)