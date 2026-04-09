---
published: false
---

# 1. Project title

**Misalignment Sensitivity and CD Error in an Inspection Imaging System**

---

# 2. Main goal

Show that small perturbations in system elements such as:

- objective decenter / tilt
    
- tube lens decenter / tilt
    
- camera sensor tilt / shift
    
- spacing errors
    

can produce:

- WFE increase
    
- MTF loss
    
- chief-ray / telecentricity error
    
- image shift / distortion
    
- defocus-like blur
    
- CD bias
    

Then show:

- which perturbations matter most
    
- why they matter physically
    
- how to reduce sensitivity
    

---

# 3. Recommended system setup

Keep the system simple but realistic:

### Optical chain

Object (wafer line-space or edge target)  
→ objective lens  
→ collimated space  
→ tube lens  
→ camera sensor

### Suggested baseline

- 5× or 10× system
    
- objective NA around 0.14–0.20
    
- infinity-corrected architecture
    
- one wavelength first, maybe 532 nm
    
- reflected-light imaging
    
- simple binary target on object plane
    

This is easier to explain than a very high-NA microscope objective.

---

# 4. High-level workflow

I would structure the project in **six stages**.

---

## Stage 1: Build and validate nominal system

First define the nominal, aligned system.

### Show:

- optical layout
    
- nominal specs
    
- spot / WFE / MTF
    
- magnification
    
- telecentricity or chief-ray angle
    
- nominal image of line/space target
    

### Deliverables

- system diagram
    
- table of design specs
    
- nominal performance plots
    

This gives the reader a clear baseline.

---

## Stage 2: Define perturbation model

Then define the mechanical / alignment perturbations.

### Perturbations to include

For each component, define one variable at a time first.

### Objective

- decenter X/Y
    
- tilt X/Y
    
- axial shift Z
    

### Tube lens

- decenter X/Y
    
- tilt X/Y
    
- axial shift Z
    

### Camera / sensor

- sensor tilt
    
- sensor axial defocus
    
- sensor lateral shift
    

Do not start with too many at once.  
First do **single-parameter sensitivity**.

### Example perturbation ranges

- decenter: ±10, ±25, ±50 µm
    
- tilt: ±0.05°, ±0.1°, ±0.2°
    
- axial shift: ±10, ±25, ±50 µm
    

Use ranges that feel physically believable.

---

## Stage 3: Optical impact analysis

For each perturbation, quantify optical degradation.

### Metrics

Use a small set of meaningful metrics:

- RMS wavefront error
    
- MTF at key spatial frequency
    
- centroid shift / image shift
    
- chief ray angle at image
    
- distortion or magnification change
    
- best focus shift
    

### Best plotting style

For each perturbation:

- x-axis = perturbation amount
    
- y-axis = metric
    

Example:

- objective tilt vs RMS WFE
    
- tube lens decenter vs image shift
    
- sensor tilt vs edge MTF
    

### Important point

Separate effects into two categories:

### A. Symmetric degradation

- blur
    
- defocus
    
- spherical / astigmatic-like degradation
    

### B. Asymmetric degradation

- coma
    
- skewed edge
    
- image shift
    
- edge-position bias
    

This is important because **CD error often comes more from asymmetric image change than from pure blur**.

---

## Stage 4: CD measurement simulation

This is the most important part.

You want to connect optical perturbation to a **metrology error**, not just optical quality.

### Use a simple target

Use one of these:

- binary line/space
    
- isolated edge
    
- contact hole
    
- bar target
    

Simplest is:

- **1D line/space or single edge**
    

### Workflow

1. Generate nominal image or line spread / edge spread
    
2. Apply perturbation
    
3. Extract intensity profile
    
4. Compute measured edge position using threshold or derivative criterion
    
5. Compute CD from two measured edges
    
6. Compare against nominal CD
    

### Output

ΔCD=CDmeasured−CDtrueΔCD=CDmeasured​−CDtrue​

This makes the project much stronger than just showing MTF.

### Very useful decomposition

For each perturbation, separate:

- image quality loss
    
- image position shift
    
- CD bias
    

Because some perturbations may give little MTF loss but still large CD bias.

---

## Stage 5: Sensitivity ranking and physical interpretation

After simulating all perturbations, summarize which are most dangerous.

### Make a dashboard table

Columns like:

|Perturbation|Main aberration effect|Main image effect|CD sensitivity|Severity|
|---|---|---|---|---|

Example:

- objective tilt → coma / field tilt → asymmetric edge shift → high CD sensitivity
    
- tube lens decenter → image shift / coma → moderate CD sensitivity
    
- sensor tilt → focus gradient across field → field-dependent CD bias
    

### Also rank with normalized sensitivity

For example:

SCD=∂CD∂pSCD​=∂p∂CD​

where pp is perturbation.

Examples:

- nm CD error / µm decenter
    
- nm CD error / degree tilt
    

This makes it look like real engineering.

---

## Stage 6: Desensitization / mitigation

This is where the project becomes excellent.

After showing the sensitivities, propose ways to reduce them.

### Options

- reduce chief-ray angle sensitivity
    
- adjust spacing or stop position
    
- redesign objective / tube lens balance
    
- tighten one mechanical tolerance and relax others
    
- choose more robust focus criterion
    
- use calibration or software correction for image shift
    
- use alignment compensation
    

### Strongest story

Compare:

### Version A

Nominal design with strong sensitivity

### Version B

Modified / desensitized design

Then show:

- lower WFE sensitivity
    
- lower MTF sensitivity
    
- lower CD bias sensitivity
    

That ties directly into your desensitization theme.

---

# 5. Best project narrative

A good narrative would be:

### Part 1 — Nominal system

“I designed an inspection-style infinity optical system and verified nominal imaging performance.”

### Part 2 — Perturbation sensitivity

“I introduced realistic misalignment errors in the objective, tube lens, and camera.”

### Part 3 — Image and metrology consequences

“I quantified not only image-quality degradation but also the resulting CD measurement error.”

### Part 4 — Engineering insight

“I identified the most critical perturbations and explained their physical mechanisms.”

### Part 5 — Improvement

“I proposed and demonstrated strategies for desensitization.”

That reads like a real engineering case study.

---

# 6. Minimum set of perturbations to include

To keep scope manageable, I would start with just **five**:

- objective tilt
    
- objective decenter
    
- tube lens tilt
    
- tube lens decenter
    
- sensor tilt
    

That is already enough for a strong project.

Then add:

- sensor defocus
    
- objective-to-tube spacing error
    

only if needed.

---

# 7. Minimum set of metrics

Do not overload it.

Use these four first:

- RMS WFE
    
- MTF at one relevant frequency
    
- image/edge position shift
    
- CD error
    

This is enough.

---

# 8. Suggested figures

A strong report would include:

### Figure 1

System layout and nominal specs

### Figure 2

Nominal image and edge profile

### Figure 3

Objective tilt sensitivity: WFE, MTF, CD error

### Figure 4

Tube lens decenter sensitivity: image shift and CD error

### Figure 5

Sensor tilt effect across field

### Figure 6

Sensitivity ranking chart

### Figure 7

Before/after desensitization comparison

---

# 9. Very important interpretation point

Do not say only:

> misalignment worsens image quality

That is too generic.

Instead say things like:

- **Objective tilt mainly induces asymmetric aberration, which shifts the apparent edge location and directly biases CD extraction.**
    
- **Tube lens decenter can create image displacement and field-dependent coma, producing measurement errors even when nominal contrast remains acceptable.**
    
- **Sensor tilt introduces focus variation across the field, causing location-dependent CD bias.**
    

That is the level interviewers will like.

---

# 10. Recommended final project structure

I would make the report / portfolio page like this:

## A. Motivation

Why alignment sensitivity matters in metrology

## B. System architecture

Objective + tube lens + sensor model

## C. Nominal performance

WFE, MTF, image formation

## D. Perturbation model

Tilt, decenter, spacing definitions

## E. Optical sensitivity results

Metric changes versus perturbation

## F. CD metrology impact

Measured CD bias versus perturbation

## G. Sensitivity ranking

Most critical errors and why

## H. Desensitization / mitigation

Improved design or alignment strategy

## I. Key conclusions

What optical designers should control first

---

# 11. Best concise takeaway

The key claim of the project should be:

> **Mechanical misalignment does not merely reduce image sharpness; it can create systematic CD measurement bias through asymmetric aberration, image shift, and focus nonuniformity.**

That sentence is strong and professional.

---

# 12. My recommendation for scope

For the first full version, do this:

### System

5×–10× infinity system

### Perturbations

5 single-parameter perturbations

### Metrics

WFE, MTF, image shift, CD bias

### Improvement

one desensitized comparison ㅊㅇ


## Level 1

- nominal system
- perturbation 5개
- single edge: edge placement error
- isolated line: CD bias

## Level 2

- field position dependence
- sensor tilt
- best focus refinding vs fixed sensor plane

## Level 3

- periodic line/space grating
- pitch-dependent CD bias
- more realistic metrology model