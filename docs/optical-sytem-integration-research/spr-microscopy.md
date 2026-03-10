---
title: Advanced Surface Plasmon Resonance (SPR) Systems
layout: default
comments: true
nav_order: 2
parent: Optical System Integration and Research
---

# Surface Plasmon Resonance (SPR) Systems

This work focuses on the development of high-resolution imaging modalities that leverage the unique properties of Surface Plasmons (SP)—longitudinal electron density waves at metal-dielectric interfaces—to perform label-free quantification of surface states. This research was published in [Optics Communications](https://www.sciencedirect.com/science/article/pii/S0030401817308878) and [Optics Letters](https://opg.optica.org/ol/fulltext.cfm?uri=ol-43-4-959).


**1. Axial Metrology: 15 nm Precision via SPR Reflectivity Modeling**

This project involved developing a method for axial localization by mapping reflectivity changes to a layered dielectric model.

- **Physics & Simulation:** Using the SP dispersion relation, I modeled how momentum-matching conditions (ksp​=k0​sinθin​) change based on the depth of objects within an evanescent field.
- **System Design:** The setup utilized an inverted microscope with a high-NA (1.49) TIRF objective and a 632.8 nm He-Ne laser. I integrated a linear motor stage to precisely control the angle of incidence, allowing for automated scanning to find the SPR dip.
- **Analytical Modeling:** I implemented a depth extraction model based on Fresnel coefficients and the Nelder model for fitting resonance characteristics.
- **Performance:** The system achieved a precision of approximately 15 nm for axial localization, demonstrating its capability for high-accuracy surface metrology.
  
![](../images/spr-microscopy.png)
![](../images/spr-microscopy-1.png)
![](../images/spr-microscopy-2.png)

**2. Lateral Resolution Enhancement: Spatially Switched SPM (ssSPM)**

Conventional Surface Plasmon Microscopy (SPM) is limited by a long propagation length (Lsp​≈10–100μm), which typically blurs images far beyond the diffraction limit. I developed ssSPM to overcome this hardware-inherent limitation.

- **Two-Channel Momentum Sampling:** I designed a hardware-switched illumination system that captures images from two opposite incident directions while maintaining identical polarization and incidence angles.
- **Mechanical Integration:** The switching was achieved by translating a linear motor stage carrying the focusing optics, enabling rapid mechanical switching between momentum channels.
- **Signal Processing:** I implemented a minimum filtering reconstruction algorithm to compare pixel intensities across channels, effectively removing the scattering "tails" caused by SP propagation.
- **Metrology Validation:** The system was validated using resist polymer nanowires (widths from 150 nm to 7.8 μm) and 250 nm nanoparticles.
- **Engineering Impact:** This technique enhanced lateral resolution by 15 times over conventional SPM, successfully approaching the optical diffraction limit without requiring fluorescent labels.
  
  ![](../images/spr-microscopy-3.png)
  ![](../images/spr-microscopy-4.png)

**Technical Competencies Demonstrated:**

- **Optical System Design:** High-NA objective integration, TIRF/SPR setup, and laser path alignment.
- **Hardware Automation:** Precision control of motorized stages for angular scanning and channel switching.
- **Computational Optics:** Fresnel coefficient modeling, Nelder fitting algorithms, and image reconstruction via minimum filtering.
- **Nanometrology:** Characterizing sub-micron features (nanowires/nanoparticles) with nano-scale axial accuracy.