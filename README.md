# Construction-Site PPE Safety Detection - MAICEN-0526 Group 5

## Project Team

**MAICEN-0526 - Group 5**

| Team Member | Role |
|---|---|
| Dru | Group Lead |
| Shaheen | Group Member |
| Ghandoor | Group Member |
| Ahamed | Group Member |

This project, including its experimental work, analysis, documentation and associated deliverables, was completed collaboratively by **MAICEN-0526 Group 5**.

---

## Project Overview

This project investigates computer-vision-based detection of Personal Protective Equipment (PPE) and selected safety conditions on construction sites using YOLO11 object detection.

The work follows an iterative experimental workflow rather than treating model training as a single-step exercise:

**Baseline model (M1) -> external challenge testing -> error analysis -> targeted augmentation -> refined model (M2) -> controlled comparison -> limitations and governance analysis**

The objective is not only to obtain strong held-out performance, but also to investigate model generalisation, failure modes, reproducibility and responsible use in a safety-related context.

---

## Dataset

The project uses the public **Site Construction Safety** object-detection dataset obtained through Roboflow Universe and forked into the project workspace for experimentation.

- Source images: **3,858**
- Task: **Object Detection**
- Classes: **7**
- Licence: **CC BY 4.0**
- Image preprocessing: **640 x 640, Fit with black-edge padding**

### Classes

1. Person
2. Helmet
3. Non-Helmet
4. Vest
5. Gloves
6. Shoes
7. bare-arms

The class ontology is important when interpreting predictions. The dataset contains an explicit `Non-Helmet` class, but does not contain equivalent `Non-Vest`, `Non-Gloves`, or `Non-Shoes` classes. Therefore, absence of a positive PPE detection must not automatically be interpreted as proof of a safety violation.

---

## Experimental Design

Three dataset versions were used as part of the experimental progression.

| Version | Purpose | Total Images | Training | Validation | Test | Status |
|---|---|---:|---:|---:|---:|---|
| V1 / M1 | Baseline | 3,858 | 3,287 | 340 | 231 | Trained and evaluated |
| V2 | Higher-augmentation experimental configuration | 10,432 | 9,861 | 340 | 231 | Configuration evaluated; not trained |
| V3 / M2 | Resource-optimised targeted augmentation | 7,145 | 6,574 | 340 | 231 | Trained and evaluated |

The absolute validation and test sets remained unchanged for M1 and M2. This supports a controlled comparison between the baseline and augmented experiments.

---

## M1 - Baseline Experiment

### Configuration

- Architecture: **YOLO11 Object Detection**
- Model size: **Nano**
- Input resolution: **640 x 640**
- Preprocessing: **Fit with black-edge padding**
- Augmentation: **None**

### Headline Results

| Metric | M1 |
|---|---:|
| mAP@50 | 96.9% |
| Precision | 97.1% |
| Recall | 94.2% |
| F1 | 95.6% |

### M1 Test Performance by Class

| Class | mAP@50 |
|---|---:|
| All | 97% |
| Gloves | 98% |
| Helmet | 99% |
| Non-Helmet | 97% |
| Person | 99% |
| Shoes | 97% |
| Vest | 99% |
| bare-arms | 91% |

Although held-out performance was strong, `bare-arms` was the weakest class. This motivated additional external challenge testing.

---

## External Challenge Testing

M1 was tested on external images that were not part of the source dataset. A fixed **50% confidence threshold** and **50% overlap threshold** were used.

The external images intentionally included conditions such as:

- partial occlusion
- multiple workers
- workers without helmets
- exposed arms
- close-up PPE
- small/background workers
- unusual framing
- footwear-related scenarios

External testing revealed that high held-out mAP did not guarantee equivalent performance on unfamiliar real-world images.

Examples of observed failure modes included:

- missed `Non-Helmet` detection for a visible worker without a helmet
- missed `bare-arms` detection
- missed `Gloves` detection in a close-up image
- missed people when workers were small, partially occluded or in the background
- complete missed detections in some unusual external scenarios

These tests are treated as **controlled qualitative external challenge tests**, rather than formal mAP evaluation, because the external images were not prepared as a fully annotated benchmark dataset.

---

## Error Analysis

| Error | Condition | Expected | Observed Issue | Likely Challenge | Proposed Mitigation |
|---|---|---|---|---|---|
| E01 | Worker without helmet | Non-Helmet | Missed detection | Domain shift | More targeted examples |
| E02 | Exposed arms | bare-arms | Missed detection | Appearance variation | Augmentation and additional data |
| E03 | Close-up glove | Gloves | Missed detection | Crop/scale variation | Crop augmentation |
| E04 | Background worker | Person | Missed detection | Small-object detection | Additional data / higher-resolution investigation |
| E05 | Unusual viewpoint | Person/PPE | Missed detection | Camera-angle variation | Viewpoint augmentation |

This error analysis informed the M2 experimental design.

---

## V2 - Higher-Augmentation Experimental Configuration

A higher-augmentation configuration was generated as an intermediate experiment.

It used approximately **3x training augmentation**, resulting in **10,432 total images**.

Before progressing to training, the configuration was assessed against the available computational and hosted-training resources. The substantially higher resource requirement motivated a more resource-efficient experimental configuration.

V2 is therefore retained as part of the experimental record rather than being represented as a failed model.

---

## M2 - Targeted Augmentation Experiment

M2 retained the same underlying source dataset, preprocessing approach, model family and held-out validation/test sets while introducing targeted augmentation.

### Augmentations

- Horizontal flip
- Random crop: **0% to 10%**
- Rotation: **-15 degrees to +15 degrees**
- Brightness: **-15% to +15%**
- Augmentation multiplier: **2x**

### M2 Dataset

- Total images: **7,145**
- Training: **6,574**
- Validation: **340**
- Test: **231**

### Model

- Architecture: **YOLO11 Object Detection**
- Model size: **Nano**
- Initial checkpoint: **COCO**

### Headline Results

| Metric | M1 | M2 | Change |
|---|---:|---:|---:|
| mAP@50 | 96.9% | 96.7% | -0.2 pp |
| Precision | 97.1% | 95.7% | -1.4 pp |
| Recall | 94.2% | 94.8% | +0.6 pp |
| F1 | 95.6% | 95.2% | -0.4 pp |

### Held-Out Test Performance by Class

| Class | M1 | M2 | Change |
|---|---:|---:|---:|
| All | 97% | 97% | 0 pp |
| Gloves | 98% | 97% | -1 pp |
| Helmet | 99% | 99% | 0 pp |
| Non-Helmet | 97% | 96% | -1 pp |
| Person | 99% | 99% | 0 pp |
| Shoes | 97% | 97% | 0 pp |
| Vest | 99% | 99% | 0 pp |
| bare-arms | 91% | 93% | +2 pp |

---

## Interpretation

Targeted augmentation did not produce a universal improvement across all metrics.

Overall held-out test performance remained approximately stable. Recall increased from **94.2% to 94.8%**, while precision decreased from **97.1% to 95.7%**. The weakest M1 test class, `bare-arms`, improved from **91% to 93% mAP@50**.

This demonstrates why model evaluation should consider class-level behaviour and error characteristics rather than relying on a single headline metric.

In a safety-monitoring context, improved recall can be relevant because missed detections may have practical consequences. However, this experiment does not establish that M2 is universally superior to M1.

---

## External Validation Limitation

The M1 baseline was additionally evaluated using external challenge images to investigate generalisation beyond the source dataset. These tests identified practical failure modes and informed the targeted augmentation strategy used for M2.

M2 was subsequently evaluated against the unchanged held-out validation and test sets, enabling a controlled comparison with M1.

Repeating the external challenge-image evaluation for M2 was outside the available hosted inference allocation during the experimental period and is therefore identified as a recommended next validation step.

No external M2 performance claims are made without supporting inference evidence.

---

## Safety and Governance

This project demonstrates an experimental PPE detection system and should **not** be treated as an authoritative construction-site safety determination system.

Important limitations include:

- False negatives may result in safety-relevant objects or conditions being missed.
- High held-out dataset performance does not guarantee equivalent real-world performance.
- Domain shift may occur across different sites, cameras, lighting conditions, worker positions and PPE designs.
- `Non-Helmet` is explicitly represented, while `Non-Vest`, `Non-Gloves` and `Non-Shoes` are not.
- Absence of a PPE detection is therefore not sufficient evidence of non-compliance.
- Human review should remain part of any safety-critical decision process.

A production compliance system could extend the detector using:

**Object detection -> worker/PPE association -> compliance rules -> human-reviewed alerts**

This separates visual detection from the higher-level decision about whether a worker is compliant.

---

## Reproducibility

The experiments used:

- Roboflow for dataset management, versioning, augmentation and hosted training
- YOLO11 Nano for object detection
- 640 x 640 image preprocessing
- fixed validation and test sets for controlled M1/M2 comparison
- fixed 50% confidence and 50% overlap thresholds during external M1 challenge testing

The repository is intended to preserve the experimental methodology, results, limitations and supporting reproducibility material.

### Planned Repository Structure

```text
construction-ppe-safety-detection-MAICEN-0526-Group-5/
├── README.md
├── notebooks/
├── results/
├── figures/
├── docs/
├── sample-images/
├── requirements.txt
└── .gitignore
