---
title: Modeling DMD Diffraction in Zemax
layout: default
comments: true
nav_order: 1
has_children: true
---



# Modeling DMD Diffraction in Zemax: From Analytical Theory to Implementation
<!-- {: .no_toc } -->

Taehwang Son 
<br> Last updated: 2026.02.18.

<!-- 
## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc} -->

This article outlines the analytical derivation and Zemax implementation of a Digital Micromirror Device (DMD) diffraction model. For the scope of this derivation, we assume an operation mode where all micromirrors are synchronized in a uniform tilt state.

The primary objective is to predict the positioning and intensity distribution of diffracted orders accurately. It is possible to get its far-field diffraction pattern with numerical calculation using the Fourier transform of the aperture. However, this requires significant computation and does not provide an intuitive understanding of DMD diffraction. Moreover, unlike applications involving arbitrary beam shaping or computer-generated holography (CGH), which require iterative phase retrieval, this model focuses on the fundamental grating physics and the "blaze grating" effect produced by the mechanical mirror tilt.

---
