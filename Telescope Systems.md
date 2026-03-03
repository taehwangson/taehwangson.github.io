
This project highlights the design and analysis of various telescope systems, ranging from large-scale space observatories to wide-field catadioptric cameras and innovative single-glass color-corrected telescopes.

## 1. The Hubble Space Telescope (Ritchey-Chrétien Aplanat)
Analyzed the optical prescription of the **Hubble Space Telescope (HST)**, a Ritchey-Chrétien aplanatic system designed to eliminate both spherical aberration and coma [1].

*   **System Specifications:** An F/24 system with a 2.4-meter aperture primary mirror [1].
*   **Optimization Strategy:** Optimized the conic constants for both mirrors to achieve an aplanatic condition, minimizing $W_{040}$ (spherical) and $W_{131}$ (coma) [1].
*   **Calculated Conic Constants:** Derived the primary mirror's conic constant as **-1.002298** and the secondary mirror as **-1.4968**, matching theoretical Ritchey-Chrétien formulas [2].
*   **Performance:** Demonstrated that the system achieves diffraction-limited imaging for a field of view of approximately **±0.04 degrees** [1].

## 2. Advanced Null Test Compensators
Designed and compared null correctors used to simulate testing environments for the HST primary mirror from its center of curvature [3, 4].

*   **Dall Null Compensator:** Utilized a single BK7 plano-convex lens to reduce spherical aberration from **177 waves RMS** (uncompensated) to less than **4 waves RMS** [5, 6].
*   **Offner Null Corrector:** Implemented a more complex relay system with a field lens at the mirror's center of curvature to correct higher-order zonal spherical aberration [4].
*   **Result:** The Offner design provided a performance improvement of **three orders of magnitude** in RMS wavefront error compared to the Dall compensator, proving it to be a superior engineering solution for precision manufacturing [7].

## 3. Wide-Field Catadioptric Systems
Developed and achromatized several catadioptric systems, exploring the trade-offs between aspheric correctors and spherical meniscus elements [8-10].

*   **Schmidt Camera:** Designed a system with an **aspheric plano-parallel plate** at the stop to correct spherical aberration over a wide **±3-degree field**, achieving diffraction-limited on-axis performance [8, 9].
*   **Maksutov Telescope:** Used a **negative meniscus lens** for spherical correction [9]. Achromatization was achieved by optimizing the lens thickness (selected **10 mm**) and surface radii, resulting in a residual focal shift between F and C wavelengths of only **0.0477 μm** [11].
*   **Houghton Camera:** Designed an **afocal doublet** using only BK7 glass to correct spherical aberration [10]. This design achieved an on-axis RMS wavefront error of **0.0075 waves**, demonstrating high-quality correction without the need for complex aspheres [12].

## 4. Schupmann Medial Telescope
Showcased an advanced design using a **single glass type (BK7)** to achieve full axial and lateral color correction [13].

*   **Design Architecture:** Combined a plano-convex objective with a **Mangin mirror** to correct longitudinal chromatic aberration and a **field lens** at the first focal point to correct lateral color [13, 14].
*   **Performance:** Optimized the system to **F/12**, achieving diffraction-limited performance at **±0.25 degrees off-axis** [14].

---

### Key Skills Demonstrated:
*   **System Optimization:** Using **Zemax** and **CODE V** to minimize Seidel aberrations and wavefront error [1, 11, 14].
*   **Catadioptric Design:** Balancing refractive and reflective powers to correct color and field curvature [11, 12].
*   **Metrology Design:** Creating null correctors to facilitate the manufacturing and testing of large aspheric mirrors [6, 7].