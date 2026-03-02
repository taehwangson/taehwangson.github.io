---
title: Camera ISP development
layout: default
comments: true
nav_order: 5
---

# Camera ISP development

Updated on Jan. 20, 2025, Taehwang Son

## Camera spec

![Camera used in the test](images/image.png)

Camera used in the test




Sensor: [JAI GO-2400C-USB](https://www.jai.com/products/go-2400c-usb) Sony IMX174,  2.3 megapixels (1920x1200), pixel 5.86μm




<img src="./images/image.png" height="300" style="display: block; margin: 0 auto;" />


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

---

## Update 2 - LSC overcorrection

Initial testing of the indoor scene revealed excessive brightness at the image periphery, indicating an overcorrection in the Lens Shading Correction (LSC) pipeline. The original LSC calibration map was derived using a captured image of a white monitor display. However, this yielded an extreme maximum gain factor exceeding 8.0 at the corners, implying that peripheral objects possessed only 12.5% of the intensity of central objects. Even accounting for aggressive optical vignetting, a gain of 8.0 is improbable for this optical design.

The discrepancy was traced to the non-Lambertian emission profile of the monitor. Most LCD displays exhibit strong directionality. When viewed at the steep angles associated with the lens's peripheral field, the perceived intensity drops sharply compared to the normal (center) view. To rectify this, a new calibration dataset was acquired using a white wall under indirect, uniform room lighting. To mitigate non-uniformities in the wall surface, multiple frames were averaged. This averaged dataset was then fitted to a 2D polynomial function to generate a smooth, mathematically robust estimate of the system’s relative illumination.

The resulting gain factor from the wall-based calibration was approximately 1.85, a significantly more realistic value for this lens-sensor combination. Both indoor and outdoor test cases confirm that the monitor-based calibration induced unnatural bright artifacts, whereas the wall-based polynomial fit provided a natural illumination balance.

This highlights the necessity of using a Lambertian source for experimental LSC calibration. Relying solely on optical simulation can be insufficient, as final relative illumination is a composite of multiple factors: mechanical/optical vignetting, the radiometric cos4(θ) law, and CMOS sensor roll-off (pixel vignetting caused by Chief Ray Angle mismatch) [[Edmund optics ref](https://www.edmundoptics.com/knowledge-center/application-notes/imaging/sensor-relative-illumination-roll-off-and-vignetting/?srsltid=AfmBOoqElK6QWmWLCYhiStaRj9mkJ2JazP_-Ci17PWzCAOg7krYwvIgl](https://www.edmundoptics.com/knowledge-center/application-notes/imaging/sensor-relative-illumination-roll-off-and-vignetting/?srsltid=AfmBOoqElK6QWmWLCYhiStaRj9mkJ2JazP_-Ci17PWzCAOg7krYwvIgl))].

**(1) Image of a painted wall with indirect lighting for calibration** 

**(Left: calibration with monitor display, right: calibration with wall)**

![image.png](images/image-26.png)

![image.png](images/image-27.png)

**(2) Indoor lighting**

**(Left: calibration with monitor display, right: calibration with wall)**

![image.png](images/image-28.png)

![image.png](images/image-29.png)

**(3) Outdoor daylight image** 

**(Left: calibration with monitor display, right: calibration with wall)**

![image.png](images/image-30.png)

![image.png](images/image-31.png)

---

# Update 3 - CFA interpolation issue

Bilinear interpolation, a non-adaptive averaging method, is prone to creating pseudo-color artifacts and zipper noise because it fails to distinguish between smooth areas and sharp edges, leading to unnatural, rapid color transitions.

In contrast, edge-directed demosaicing methods, such as VNG, overcome this limitation. They adaptively suppress these artifacts by first detecting the direction of sharp edges (high gradients) and then interpolating along that edge, thereby preserving detail and color.

![Reference: [Book] Theory and Applications of Smart Cameras, Architectural Analysis of a Baseline ISP Pipeline](images/image-32.png)

Reference: [Book] Theory and Applications of Smart Cameras, Architectural Analysis of a Baseline ISP Pipeline

(1) Bilinear interpolation

![Exp 15ms, Gain6, [JAI GO-2400C-USB](images/image-33.png)

Exp 15ms, Gain6, [JAI GO-2400C-USB](https://www.jai.com/products/go-2400c-usb)

![Zoom-in image (left bottom)](images/image-34.png)

Zoom-in image (left bottom)

(2) Variable Number of Gradients (VNG) interpolation

![image.png](images/image-35.png)

![image.png](images/image-36.png)

(3) Reference image taken by Galaxy A8 star

![image.png](images/image-37.png)

![image.png](images/image-38.png)

Although VNG interpolation removes color noise where color changes rapidly, it does not remove chromatic change from single-pixel bright spots. This is due to color aliasing, where the bright dot is composed of a few pixels and causes sampling issues under the Bayer pattern. DDFPD uses the local chromatic components (R−G and B−G differences) for both the horizontally and vertically interpolated results. It selects the result that shows greater local color smoothness/homogeneity. However, the example below shows that some degree of false color light spots, even after DDFPD interpolation. Further correction will be discussed later with chroma filtering.

(1) Variable Number of Gradients (VNG) interpolation

![Exp 15ms, Gain6, [JAI GO-2400C-USB](images/image-35.png)

Exp 15ms, Gain6, [JAI GO-2400C-USB](https://www.jai.com/products/go-2400c-usb)

![Zoom-in image (left bottom)](images/image-39.png)

Zoom-in image (left bottom)

(2) DDFPD (Demosaicing With Directional Filtering and *a posteriori* Decision) - [D. Menon 2007](https://ieeexplore.ieee.org/document/4032820)

![image.png](images/image-40.png)

![image.png](images/image-41.png)

(3) Reference image taken by Galaxy A8 star

![image.png](images/image-37.png)

![image.png](images/image-42.png)

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

![image.png](images/image-52.png)

![24 colorchecker](images/image-53.png)

24 colorchecker

![GWH applied ](images/image-54.png)

GWH applied 

**2. G-Channel Linearization with Scalar Gains**

![image.png](images/image-55.png)

![image.png](images/image-56.png)

![image.png](images/image-57.png)

**3. Linear fitting ( y intercept = 0 )**

![image.png](images/image-58.png)

![image.png](images/image-59.png)

![image.png](images/image-60.png)

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

# Update 7 - Lateral chromatic aberration correction

**Optical Simulation**

Although the specific lens used in this ISP development is unknown, its wide field of view (FOV) suggests a reverse-telephoto or fisheye lens. To estimate the potential magnitude of lateral color, a Zemax reverse-telephoto reference design was re-optimized for a focal length of f=6mm. The design serves as a representative model (field angle ±31∘, F-number 3.5), though it is not fully optimized for production.

The lateral color plots illustrate the deviation of red and blue rays relative to the green reference as the field angle increases. Blue rays exhibit more severe Lateral chromatic aberration (LCA) than red, with a maximum lateral displacement of 2.72μm at the edge of the field. Furthermore, the magnitude of LCA is sensitive to object distance: at a 250mm object position, the LCA increases to 2.97μm, a 0.25μm increase compared to the infinite focus position. However, it is important to note that the LCA measurement itself has a standard deviation of 0.87 μm. LCA measurement will be discussed further in the later part.

**Reverse telephoto F/3.5 f=6mm, FOV +- 31 deg**

![image.png](images/image-66.png)

![Lateral color plot, object at infinity, D 2.72 um at 31 deg field ( 3.62 mm image)](images/image-67.png)

Lateral color plot, object at infinity, D 2.72 um at 31 deg field ( 3.62 mm image)

![Lateral color plot, object at 250mm, D 2.97 um at 31 deg field (3.6w mm image)](images/image-68.png)

Lateral color plot, object at 250mm, D 2.97 um at 31 deg field (3.6w mm image)

![Marginal and chief rays](images/image-69.png)

Marginal and chief rays

![Chief rays for red, green, blue rays at image plane](images/image-70.png)

Chief rays for red, green, blue rays at image plane

**LCA Measurement and Correction**  

LCA was corrected using independent channel warping and non-linear resampling, following the methodology proposed by [[T.E Boult et al.](https://ieeexplore.ieee.org/document/223201)] To quantify the aberration, a dot-grid target was captured. In the presence of LCA, the centroids of the white dots shift across the RGB channels, manifesting as visible color fringes at high-contrast edges.

Centroids were calculated for each dot across all three channels. The resulting 2D vector plots show the displacement of the red and blue channels relative to the green reference. The red channel shift is negligible compared to the blue and lacks axial symmetry, suggesting that the red-channel behavior is likely dominated by manufacturing tolerances or sensor alignment rather than the nominal optical design. The measurement precision was validated by analyzing the circumferential vector components, which are theoretically zero in a rotationally symmetric system. The standard deviation of this circumferential component was 0.87μm ((=0.15 pixels x 5.85 μm/pixel).

Consequently, the red channel shift remains uncorrected in the current iteration, although correction may be feasible if a specific theoretical design model becomes available. In contrast, the blue channel exhibits a clear, monotonic radial shift consistent with the Zemax simulation. This was modeled using polynomial fitting. During the dewarping stage, each pixel coordinate is transformed according to this fit and remapped using sub-pixel interpolation.

![Dot grid pattern ](images/image-71.png)

Dot grid pattern 

![image.png](images/image-72.png)

![Red channel center coordiante shift](images/image-73.png)

Red channel center coordiante shift

![Blue channel center coordiante shift](images/image-74.png)

Blue channel center coordiante shift

![Blue channel, radial vector vs. distance from center](images/image-75.png)

Blue channel, radial vector vs. distance from center

![Blue channel, circumferential vector vs. distance from center](images/image-76.png)

Blue channel, circumferential vector vs. distance from center

![Red channel, radial vector vs. distance from center](images/image-77.png)

Red channel, radial vector vs. distance from center

![Red channel, circumferential vector vs. distance from center](images/image-78.png)

Red channel, circumferential vector vs. distance from center

The comparison images demonstrate a significant improvement in image quality following LCA correction. Prior to correction, the edges of bright regions exhibited distinct green and blue fringing. By shifting the blue channel toward the dot centers, these artifacts were substantially mitigated. While some residual coma aberration is visible in the dot grid, the overall edge acutance is improved. This enhancement is also observable in outdoor scenes, where the previously apparent blue-green color bleeding on structural edges has been visibly reduced.

 

**Zoom-in dot grid pattern (left: before LCA correction, right: after LCA correction)**

![image.png](images/image-79.png)

![image.png](images/image-80.png)

**Outdoor daylight image  (left: before LCA correction, right: after LCA correction)**

![image.png](images/image-81.png)

![image.png](images/image-82.png)

**Zoom-in outdoor daylight image, right bottom  (left: before LCA correction, right: after LCA correction)**

![image.png](images/image-83.png)

![image.png](images/image-84.png)

**Zoom-in outdoor daylight image, left top (left: before LCA correction, right: after LCA correction)**

![image.png](images/image-85.png)

![image.png](images/image-86.png)

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

# Update 9 - Luminance (Y)  channel noise filter and edge enhancement

After YUV conversion, the luminance (Y)  channel is noise-filtered to suppress high-frequency grain. This noise typically originates from dark current or readout noise, which becomes prominent when the Signal-to-Noise Ratio (SNR) is low—most notably in night-vision applications requiring high gain and extended exposure times. Even in daylight, flat regions like the sky can exhibit artifacts caused by photon shot noise. For ISP tuning, the primary challenge is balancing noise alleviation with detail preservation [[Digital Camera Technologies for Scientific Bio-Imaging. Part 3: Noise and Signal-to-Noise Ratios](https://analyticalscience.wiley.com/content/news-do/digital-camera-technologies-scientific-bio-imaging-part-3-noise-and-signal-to-noise)].

In this implementation, a [non-local mean (NLM) filter](https://www.ipol.im/pub/art/2011/bcm_nlm/) was chosen because internal testing demonstrated superior edge preservation compared to the Bilateral Filter. The screenshots below illustrate performance in outdoor scenes under varying lighting conditions. While we initially utilized the standard OpenCV recommendations (`h=10, templateWindowSize=7, searchWindowSize=21`), further fine-tuning was required to retain high-frequency details. Using a custom Python-based GUI to iterate through parameters, we identified optimal settings (`h=2, templateWindowSize=3, searchWindowSize=11`) for daylight outdoor scenes that effectively maintain fine structures, such as distant tree branches

**Outdoor, night vision, Y channel only**

![cv.fastNlMeansDenoising(), h=10, templateWindowSize=7, searchWindowSize=21](images/image-98.png)

cv.fastNlMeansDenoising(), h=10, templateWindowSize=7, searchWindowSize=21

![cv.fastNlMeansDenoising(), h=2, templateWindowSize=3, searchWindowSize=11](images/image-99.png)

cv.fastNlMeansDenoising(), h=2, templateWindowSize=3, searchWindowSize=11

**Outdoor, daylight, Y channel only**

![cv.fastNlMeansDenoising(), h=10, templateWindowSize=7, searchWindowSize=21](images/image-100.png)

cv.fastNlMeansDenoising(), h=10, templateWindowSize=7, searchWindowSize=21

![cv.fastNlMeansDenoising(), h=2, templateWindowSize=3, searchWindowSize=11](images/image-101.png)

cv.fastNlMeansDenoising(), h=2, templateWindowSize=3, searchWindowSize=11

While NLM filtering effectively suppresses noise, the non-ideal nature of the filter introduces spatial blurring and reduces edge contrast. To recover these details, Laplacian-based edge enhancement with a soft-thresholding coring strategy was employed. The coring function below applies a non-linear gain ramp to prevent the 'ringing' typically associated with steep Laplacian transitions.

$Y_{sharpened}=Y_{original}+M(|E|)*gain$

![image.png](images/image-102.png)

To specifically address 'beading artifacts'—where fine 1–2 pixel structures break into pixelated features due to high-frequency noise amplification—a 3x3 median filter was integrated into the edge processing. This median filter acts as a spatial outlier rejection step, suppressing isolated noise spikes in the edge map while preserving the structural continuity of thin tree branches. The result is a significant enhancement of branch outlines with a marked reduction in granular artifacts

**Edge enhanced without median filter, Outdoor, daylight, Y channel only**

![Edge enhancement without 3x3 median filter](images/image-103.png)

Edge enhancement without 3x3 median filter

![Edge enhancement without 3x3 median filter](images/image-104.png)

Edge enhancement without 3x3 median filter

**Edge enhanced with median filter, Outdoor, daylight, Y channel only**

![Edge enhancement with 3x3 median filter](images/image-105.png)

Edge enhancement with 3x3 median filter

![Edge enhancement with 3x3 median filter](images/image-106.png)

Edge enhancement with 3x3 median filter

**Edge enhanced with median filter, Outdoor, Night vision (Exp 16ms, Gain 6), Y channel only**

![image.png](images/image-107.png)

![image.png](images/image-108.png)

---

# Update 10- Chroma filtering

While the Luminance (Y) channel path targets stochastic grain and edge enhancement, the Chroma (Cr, Cb) channels undergo edge-based filtering to suppress chroma haziness and eliminate speckle-like false color artifacts.

Initially, a 3×3 median filter is applied to the Chroma channels to remove impulse noise. Subsequently, the pixel values are modulated according to the following relationship:

$$C_{out,r} = 0.5+(C_{in,r}-0.5)\times(1-M({\mid}E{\mid}))$$

$$C_{out,b} = 0.5+(C_{in,b}-0.5)\times(1-M({\mid}E{\mid}))$$

![image.png](images/image-102.png)

Where $ M({\mid}E{\mid})$ represents the suppression Mask derived from the Luma edge map. Under high-gradient conditions (strong edges), the term $(1-M({\mid}E{\mid}))$ approaches 0, effectively desaturating the pixel to neutral gray. Conversely, in low-gradient regions (weak edges), the term approaches 1, preserving the original chromatic integrity.

In an outdoor night scene, false color spots on vehicle surfaces are effectively neutralized using thresholds of `threshold_low=0.1, threshold_high=0.3` .

However, applying these same parameters to an outdoor daylight scene resulted in overcorrection. This was visible as unnatural "white halos" or desaturated edges on high-contrast objects, such as yellow signs and utility poles. By relaxing the thresholds to `threshold_low=0.1, threshold_high=0.6`, the edges successfully retained their original color. This demonstrates that the suppression thresholds must be adaptively tuned based on exposure time or analog gain to prevent peripheral desaturation in high-signal environments.

**Outdoor night scene**

![Correction for false color spot on vehicle surfaces ](images/image-109.png)

Correction for false color spot on vehicle surfaces 

![Before edge-based chroma filtering](images/image-110.png)

Before edge-based chroma filtering

**Outdoor day scene**

![After edge-based chroma filtering](images/image-111.png)

After edge-based chroma filtering

![white halos near yellow sign and utility pole](images/image-112.png)

white halos near yellow sign and utility pole

![image.png](images/image-113.png)

![Before edge-based chroma filtering](images/image-114.png)

Before edge-based chroma filtering

![After edge-based chroma filtering](images/image-115.png)

After edge-based chroma filtering

---

# Update 11 - Fixed pattern noise removal

**1. Introduction to Sensor Noise**

Noise in CMOS image sensors is broadly categorized into **temporal** and **spatial** components. Temporal noise includes shot noise, readout noise, and thermal noise, manifesting as signal fluctuations over time. These characteristics are typically evaluated using the Photon Transfer Curve (PTC) method, as outlined in the **EMVA 1288** guidelines [Photon transfer, J. R. Janesick, EMVA Standard 1288].

In contrast, spatial noise—commonly referred to as **Fixed Pattern Noise (FPN)**—represents the pixel-to-pixel intensity variations that persist after temporal noise is removed via frame averaging. Unlike CCDs, CMOS sensors exhibit significant FPN due to manufacturing variances in the individual transistors integrated into each pixel. While EMVA 1288 utilizes **DSNU** (Dark Signal Non-Uniformity) and **PRNU** (Photo Response Non-Uniformity) as global performance metrics, these single-value statistics do not fully characterize the pixel-level dependency on camera gain and exposure time. **PRNU was tested first but it did not produce speckle like spatial noise. Therefore, removing DSNU noise is major target.** 

**2. Proposed FPN Model**

By analyzing the mean intensity of 100-frame averaged dark images across varying gains (G) and exposure times (t), we observed a high degree of linearity (R² = 0.97). This suggests that the intensity of a pixel at coordinates (i,j) can be modeled linearly:

$I_{FPN}(i,j)=G*[S(i,j)+K(i,j)*t+O_{input}(i,j)]+O_{output}(i,j)$

where: S(i,j): The photon-induced signal, defined as:

$S(i,j)=\frac{A*E(i,j)*t*QE*G_{sys}}{hc/\lambda}$ 

note that A, E, QE, and $G_{sys}$ are sensor area, irradiance, quantum field, and system conversion factor.

K(i,j): The pixel-specific dark current generation rate.

$O_{input}(i,j)$: Fixed pattern offset generated before amplification

$O_{output}(i,j)$: Black level, used to prevent signal clipping during Analog-to-Digital Conversion (ADC).

![mean pixel value (ADU) of 100-frame average dark images w.r.t. gain and exposure time](images/image-116.png)

mean pixel value (ADU) of 100-frame average dark images w.r.t. gain and exposure time

Images below are 100x100 pixels cropped images showing linear intensity increment with respect to gain and exposure time. As we saw in mean pixel value relation, the overall intensity is increased as gain and exposure time increase. But speckle patterns are major correction targets, which are potentially caused by manufacting errors.

![100x100 pixel cropped original dark image with different gains and exposure times ](images/image-117.png)

100x100 pixel cropped original dark image with different gains and exposure times 

**3. Calibration method**

To isolate the model parameters, a two-step calibration was performed.

**3.1. Fixed Pattern Offset, $O_{input}(i,j)$**

First, images were captured at a minimum exposure time (t=10μs) to suppress the contributions of S(i,j) and K(i,j). In this regime, the model simplifies to a linear function of gain:

$I_{FPN}(i,j)≈G⋅O_{input}(i,j)+O_{output}(i,j)$

Plotting intensity versus gain allowed us to extract $O_{input}(i,j)$ as the slope of the linear fit. While most pixels remained near the baseline black level, specific "hot pixels" exhibited significant slopes, identifying them as high-offset outliers.

![Calibration for $O_{input}(i,j)$. The graph shows $I_{FPN}(i,j)=G(i,j)*O_{input}(i,j)+O_{output}(i,j)$ relation because t is only 10us. The slope of linear fit line is $O_{input}(i,j)$](images/image-118.png)

Calibration for $O_{input}(i,j)$. The graph shows $I_{FPN}(i,j)=G(i,j)*O_{input}(i,j)+O_{output}(i,j)$ relation because t is only 10us. The slope of linear fit line is $O_{input}(i,j)$

**3.2. Dark Current Rate, K(i,j)** 

With $O_{input}$ characterized, the dark current rate was isolated by varying the exposure time at a fixed gain.

$I_{FPN}(i,j)=(G⋅K)t+O_{output}(i,j)$

The slope of the intensity versus exposure time plot yields the dark current rate K(i,j). This parameter is critical for night vision applications, where long integration times amplify thermal leakage.

![Calibration for k(i,j). The left graph shows $I_{FPN}(i,j)=G(i,j)*[K(i,j)*t]+O_{output}(i,j)$ relation because $O_{output}(i,j)$  is corrected. The slope of linear fit line is K(i,j).](images/image-119.png)

Calibration for k(i,j). The left graph shows $I_{FPN}(i,j)=G(i,j)*[K(i,j)*t]+O_{output}(i,j)$ relation because $O_{output}(i,j)$  is corrected. The slope of linear fit line is K(i,j).

**4. Result and discussion**

In standard operating conditions, spatial noise is often masked by temporal readout noise. However, in high-gain night vision applications, FPN frequently exceeds the temporal noise floor ($3σ_{temporal}$), appearing as false signals or "speckle." The effectiveness of the proposed correction is demonstrated through histogram analysis. Note that the histogram below shows pixel value distribution from original dark images at gain 8, $O_{input}(i,j)$ corrected images, and $O_{input}(i,j)$ and k(i,j) corrected images and the vertical lines correspond to $3σ_{temporal}$. The effective noise removal can be confirmed with dark images after $O_{input}(i,j)$ and k(i,j) corrected images. The raw dark signal distribution is significantly improved after correcting for $O_{input}$ and K(i,j). Notably, a small population of "impulse noise" remains; these are attributed to pixels reaching the hardware saturation limit, where the linear model no longer applies.

![image.png](images/image-120.png)

![100x100 pixel cropped images after $O_{input}(i,j)$ correction](images/image-121.png)

100x100 pixel cropped images after $O_{input}(i,j)$ correction

![100x100 pixel cropped images after $O_{input}(i,j)$ and k(i,j) corrected images](images/image-122.png)

100x100 pixel cropped images after $O_{input}(i,j)$ and k(i,j) corrected images

Finally, the correction was applied to a representative night vision outdoor scene. The removal of FPN eliminated noticeable impulse noise that would otherwise trigger false detections in chroma filters or computer vision algorithms.

![night vision image (gain 6, exposure time 15ms)](images/image-123.png)

night vision image (gain 6, exposure time 15ms)

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