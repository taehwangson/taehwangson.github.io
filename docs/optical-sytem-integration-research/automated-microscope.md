---
title: Automated Multi-Channel Fluorescence Microscope
layout: default
comments: true
nav_order: 1
parent: Optical system integration and research
---


# Automated Multi-Channel Fluorescence Microscope

This project focused on the complete lifecycle of developing a high-performance, automated benchtop imaging system tailored for labeled cells captured on PDMS chips. The development journey progressed from rigorous optical simulation to hardware prototyping and full software integration. This research was published in [Small]([[https://onlinelibrary.wiley.com/doi/abs/10.1002/smll.202507120]]) in 2025. 

**1. Optical Design & Zemax Optimization**

The system was engineered to handle multi-channel fluorescence. Key design milestones included:

- **Illumination Strategy:** Using Zemax simulations, I compared single-LED and 4-LED designs. While the 1-LED system offered better efficiency, the 4-LED configuration provided two times brighter illumination and eliminated "LED images" (artifacts) in the field.
- **Critical Parameter Tuning:** I optimized the distance between the LED and condensing lens (R1​≈7 mm), which was identified as a critical factor for maximizing illumination intensity and achieving an artifact-free field.
  ![[images/automated-microscope-1.png]]
  ![[images/automated-microscope-2.png]]
  ![[images/automated-microscope-3.png]]
![[images/automated-microscope-4.png]]
**2. Hardware Prototyping & Integration**

The physical system was designed for a compact footprint (304×152×254 mm) and integrated several critical mechanical components:

- **Fluidic Integration:** A custom fluidic system was integrated for priming samples with positive pressure, essential for the PDMS chip application.
- **Arduino automation:** A PCB board equipped with Arduino Nano, LED drivers, a servo motor and BJT transistors is used to electronically switch LEDs. Arduino is connected PC for GUI-based LED control
  
  ![[images/automated-microscope-5.png]]
![[images/automated-microscope-6.png]]


**3. Automation & Custom Software (Python/GUI)**

To make the system user-friendly and fully automated, I developed a custom Python-based GUI (Tkinter).

- **System Control:** The software provided a "Live Mode," automated LED selection, and motorized Z-stage control for precise focusing.
- **Automated Data Analysis:** I implemented an image processing pipeline using Otsu thresholding to identify and label single cells, allowing for the automated extraction of fluorescence intensities across different channels.
  ![[media11.gif]]

**4. Results and Performance Validation**

The final system demonstrated significant performance gains over legacy designs (such as "CytoPAN"):

- **SNR Enhancement:** Achieved a ~20-fold enhancement in Signal-to-Noise Ratio (SNR) for AF488, AF555, and AF647 channels.
- **Image Quality:** Automated identification and labeling were validated with a Spearman correlation of r=0.73 when comparing marker intensities.
  ![[images/automated-microscope-7.png]]![[images/automated-microscope-8.png]]
  ![[images/automated-microscope-9.png]]
  ![[images/automated-microscope-11.png]]