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

The project follows an iterative experimental workflow rather than treating model training as a single-step exercise:

**Baseline model (M1) -> external challenge testing -> error analysis -> targeted augmentation -> refined model (M2) -> controlled comparison -> limitations and governance analysis**

The objective is not only to obtain strong held-out performance, but also to investigate model generalisation, failure modes, reproducibility and responsible use in a safety-related AECO context.

---

## AECO Problem and Success Criteria

Construction sites contain dynamic working conditions, multiple workers, varying camera viewpoints, occlusion, lighting variation and different PPE configurations. Manual observation remains essential, but computer vision may assist safety personnel by identifying observations that warrant review.

This project therefore investigates whether an object-detection model can identify:

- workers;
- helmets;
- workers without helmets;
- safety vests;
- gloves;
- shoes; and
- exposed arms.

The project is considered successful as an experimental proof-of-concept if it demonstrates:

1. a documented and reproducible dataset and modelling workflow;
2. strong held-out detection performance;
3. transparent comparison between a baseline and a targeted-augmentation experiment;
4. external challenge testing to investigate domain shift;
5. documented failure modes and improvement priorities; and
6. appropriate governance, licensing and human-oversight considerations.

The model is **not** intended to make autonomous safety-compliance decisions.

---

## Dataset

The source dataset was obtained through **Roboflow Universe** and contains:

- **3,858 annotated images**
- **7 object-detection classes**
- **0 unannotated images**

### Class List

| Class | Annotated Instances |
|---|---:|
| Person | 11,030 |
| Helmet | 5,068 |
| Non-Helmet | 1,431 |
| Vest | 9,764 |
| Gloves | 9,436 |
| Shoes | 7,173 |
| bare-arms | 1,310 |

### Baseline Dataset Split

| Split | Images | Approximate Share |
|---|---:|---:|
| Training | 3,287 | 85% |
| Validation | 340 | 9% |
| Test | 231 | 6% |
| **Total** | **3,858** | **100%** |

### Baseline Preprocessing

- Resize to **640 x 640**
- Resize method: **Fit**
- Black-edge padding used to preserve aspect ratio
- No additional augmentation applied to the M1 baseline dataset

The source dataset is identified as licensed under **CC BY 4.0**. Dataset attribution and licensing considerations are discussed further in `docs/governance_and_licensing.md`.

---

## Experimental Design

Two YOLO11 Nano experiments were compared.

| Experiment | Dataset | Training Images | Validation Images | Test Images | Purpose |
|---|---|---:|---:|---:|---|
| M1 | Baseline Version 1 | 3,287 | 340 | 231 | Establish baseline performance |
| M2 | Targeted Augmentation - 2x | 6,574 | 340 | 231 | Evaluate targeted augmentation |

The validation and test partitions remained unchanged between M1 and M2. This supports a controlled comparison because the principal experimental change was the targeted augmentation applied to the M2 training data.

---

## M1 - Baseline Model

### Configuration

- Architecture: **YOLO11 Nano Object Detection**
- Starting checkpoint: **MS COCO**
- Dataset: **Roboflow Version 1**
- Input size: **640 x 640**
- Additional dataset augmentation: **None**
- Training environment: **Roboflow-hosted training**
- Approximate training duration: **1 hour**
- Final training graph observation: **Epoch 299**

Model identifier:

`site-construction-safety-1uiqh-1-yolov11n-t1`

### M1 Performance

| Metric | M1 Result |
|---|---:|
| mAP50 | **96.93%** |
| mAP50-95 | **80.63%** |
| Precision | **97.1%** |
| Recall | **94.2%** |
| F1 | **95.6%** |

### M1 Held-Out Test mAP50 by Class

| Class | mAP50 |
|---|---:|
| All | 97% |
| Gloves | 98% |
| Helmet | 99% |
| Non-Helmet | 97% |
| Person | 99% |
| Shoes | 97% |
| Vest | 99% |
| bare-arms | 91% |

The **bare-arms** class was the weakest M1 class in the held-out test results.

Strong held-out performance does not mean that the model has approximately 97% real-world safety accuracy. External challenge testing was therefore used to investigate generalisation beyond the source dataset.

---

## External Challenge Testing

The M1 baseline model was tested qualitatively on external images that were not part of the source dataset.

### Fixed Test Settings

- Confidence threshold: **50%**
- Overlap threshold: **50%**
- Opacity: **75%**
- Same settings used across the challenge tests
- Images were not deliberately cropped or modified to force detections

The external images were not manually annotated as a formal ground-truth benchmark. These results are therefore treated as **qualitative challenge-test evidence**, not as a second formal mAP evaluation.

### Key Observations

External testing demonstrated that strong held-out metrics did not eliminate generalisation failures.

Observed weaknesses included:

- missed Non-Helmet conditions;
- missed bare-arms detections;
- missed Gloves detections;
- missed or inconsistent detection of small/background workers;
- difficulty with partially visible or cropped subjects;
- sensitivity to different scene composition and viewpoints; and
- domain shift between the source dataset and external real-world imagery.

Five preserved external challenge-test screenshots are included in the `results/` directory.

---

## Error Analysis

Three clear false-negative patterns were preserved and documented:

| Error | Observation | Error Type | Likely Cause | Improvement Direction |
|---|---|---|---|---|
| FN-01 | Visible PPE/safety-related object or condition missed at the fixed threshold | False Negative | Domain shift and appearance variation | Add representative difficult examples |
| FN-02 | Relevant objects/persons missed in a complex scaffolding/construction scene | False Negative | Occlusion, small-object scale and scene complexity | Add occluded and varied-viewpoint examples |
| FN-03 | Distant/background person not consistently detected | False Negative | Small object size and distance | Increase distant-worker representation |

Additional challenge testing identified weaknesses involving **Non-Helmet, bare-arms and Gloves**.

### False-Positive Evidence Limitation

Three defensible false-positive examples were not established in the preserved external challenge-test evidence.

No false-positive examples have been fabricated or retrospectively relabelled simply to satisfy an evidence count. Future evaluation should use a manually annotated external test set so that false positives and false negatives can be systematically quantified.

Detailed analysis is provided in:

`docs/error_analysis.md`

---

## M2 - Targeted Augmentation Experiment

M2 was developed after reviewing M1 external challenge-test behaviour.

The objective was to investigate whether targeted augmentation could improve robustness to partial framing, orientation, camera-angle and lighting variation while maintaining strong held-out performance.

### M2 Dataset

Dataset version:

**M2 Targeted Augmentation - 2x**

Dataset size:

- Training images: **6,574**
- Validation images: **340**
- Test images: **231**
- Total generated dataset size: **7,145**

The original validation and test sets were retained.

### Targeted Augmentations

- Horizontal Flip
- Random Crop: **0% to 10%**
- Rotation: **-15 degrees to +15 degrees**
- Brightness: **-15% to +15%**
- Augmentation multiplier: **2x**

### M2 Model Configuration

- Architecture: **YOLO11 Nano Object Detection**
- Starting checkpoint: **MS COCO**
- Input size: **640 x 640**
- Training environment: **Roboflow-hosted training**
- Approximate training duration: **6 hours**
- Final training graph observation: **Epoch 264**

Model identifier:

`site-construction-safety-1uiqh-3-yolov11n-t1`

### M2 Performance

| Metric | M2 Result |
|---|---:|
| mAP50 | **96.69%** |
| mAP50-95 | **78.58%** |
| Precision | **95.7%** |
| Recall | **94.8%** |
| F1 | **95.2%** |

### M2 Held-Out Test mAP50 by Class

| Class | mAP50 |
|---|---:|
| All | 97% |
| Gloves | 97% |
| Helmet | 99% |
| Non-Helmet | 96% |
| Person | 99% |
| Shoes | 97% |
| Vest | 99% |
| bare-arms | 93% |

---

## M1 versus M2 Comparison

| Metric | M1 | M2 | Change |
|---|---:|---:|---:|
| mAP50 | 96.93% | 96.69% | -0.24 pp |
| mAP50-95 | 80.63% | 78.58% | -2.05 pp |
| Precision | 97.1% | 95.7% | -1.4 pp |
| Recall | 94.2% | 94.8% | +0.6 pp |
| F1 | 95.6% | 95.2% | -0.4 pp |

### Class-Level Test Comparison

| Class | M1 | M2 | Change |
|---|---:|---:|---:|
| Gloves | 98% | 97% | -1 pp |
| Helmet | 99% | 99% | 0 pp |
| Non-Helmet | 97% | 96% | -1 pp |
| Person | 99% | 99% | 0 pp |
| Shoes | 97% | 97% | 0 pp |
| Vest | 99% | 99% | 0 pp |
| bare-arms | 91% | 93% | +2 pp |

### Interpretation

M2 did **not** produce a universal improvement.

Positive changes included:

- Recall increased by **0.6 percentage points**
- bare-arms held-out test mAP50 increased by **2 percentage points**

Trade-offs included:

- mAP50 decreased slightly by **0.24 percentage points**
- mAP50-95 decreased by **2.05 percentage points**
- Precision decreased by **1.4 percentage points**
- Gloves test mAP50 decreased by **1 percentage point**
- Non-Helmet test mAP50 decreased by **1 percentage point**

The results therefore demonstrate an experimental trade-off rather than evidence that M2 is universally superior to M1.

M2 was not subsequently tested using the same external challenge-test protocol. Accordingly, **no claim is made that M2 improved external real-world generalisation**.

---

## Prioritised Improvement Plan

Three improvements are prioritised for future development.

### 1. Increase Representative Targeted Training Data

Collect and annotate additional difficult examples, particularly:

- Non-Helmet conditions;
- exposed arms;
- gloves;
- different PPE designs;
- different construction environments; and
- varied worker appearances and scene conditions.

### 2. Improve Small-Object, Crop and Viewpoint Robustness

Increase representation of:

- distant workers;
- partially visible workers;
- occlusion;
- unusual camera angles;
- close-up/cropped PPE;
- varied lighting; and
- different image resolutions.

### 3. Improve the Compliance Ontology and Rule Layer

The current ontology contains:

`Person`, `Helmet`, `Non-Helmet`, `Vest`, `Gloves`, `Shoes`, `bare-arms`

It does **not** contain explicit:

- Non-Vest
- Non-Gloves
- Non-Shoes

classes.

Therefore, absence of a Vest, Gloves or Shoes detection must **not** automatically be interpreted as proof of non-compliance.

A practical system would require a workflow such as:

**worker detection -> PPE detection -> worker/PPE association -> compliance rules -> confidence/uncertainty handling -> alert/review**

---

## Evidence and Results

The `results/` directory contains selected evidence supporting the experiment, including:

- M1 model performance summary;
- M2 model performance summary;
- M1 training curves;
- M2 training curves;
- M1 baseline configuration evidence;
- M2 targeted-augmentation configuration evidence;
- three annotation examples; and
- five external challenge-test prediction examples.

Detailed interpretation is provided in the accompanying documentation rather than relying on screenshots alone.

### Validation-Prediction Evidence Qualification

The completed Roboflow-hosted training runs generated quantitative evaluation results using the predefined held-out validation and test partitions.

The baseline dataset contained:

- **340 validation images**
- **231 test images**

A YOLO11-format export preserving the dataset partitions is retained separately within the project submission evidence.

Ten individual validation-prediction screenshots were not subsequently generated because the available Roboflow hosted credits had been exhausted.

Rather than recreate, simulate or misrepresent those screenshots, this limitation is explicitly disclosed.

Supporting evaluation evidence includes:

- preserved dataset version and split;
- 340-image validation partition;
- 231-image test partition;
- M1 and M2 training/evaluation metrics;
- class-level held-out test results;
- training curves;
- annotation examples;
- controlled M1-versus-M2 comparison; and
- genuine external challenge-test predictions.

The absence of the requested individual validation-prediction screenshots remains an explicit evidence limitation.

---

## Reproducibility

The primary reproducibility notebook is:

`notebooks/MAICEN_0526_Group_5_Construction_PPE_Safety_Detection_Reproducibility_v0_2.ipynb`

A corresponding Python representation is also provided:

`notebooks/maicen_0526_group_5_construction_ppe_safety_detection_reproducibility_v0_2.py`

### Verified Notebook Environment

The successfully executed notebook recorded:

- Python: **3.13.15**
- Environment: **Linux x86_64**
- Ultralytics: **8.4.156**
- Roboflow: **1.5.0**

The repository also includes:

`requirements.txt`

### Important Reproducibility Boundary

The notebook does **not** rerun the historical Roboflow-hosted M1 and M2 training jobs.

Instead, it:

- records the historical experimental configurations;
- records the original results;
- verifies the executable environment;
- reproduces comparison calculations;
- documents the experimental interpretation; and
- records limitations and governance considerations.

Historical Roboflow results are therefore clearly distinguished from newly executed notebook calculations.

Where a historical training parameter was not preserved in the available evidence, it is not retrospectively invented.

---

## How to Reproduce

The analytical workflow is designed for **Google Colab**, avoiding the need for a local software installation.

1. Clone or download this GitHub repository.
2. Navigate to the `notebooks/` directory.
3. Open `MAICEN_0526_Group_5_Construction_PPE_Safety_Detection_Reproducibility_v0_2.ipynb`.
4. Open or upload the notebook in Google Colab.
5. Start with a fresh Colab runtime.
6. Run the notebook from the beginning using **Run all**.
7. Allow the package-installation cell to complete.
8. Restart the runtime if requested after package installation.
9. Run all cells again.
10. Confirm the environment-verification output.
11. Review the recorded M1 and M2 configurations and results.
12. Run the metric-comparison cells.
13. Review the error-analysis, governance and reproducibility limitations.

The notebook execution time is substantially shorter than the historical hosted model-training times because it verifies and analyses the preserved experiments rather than retraining M1 and M2.

---

## Governance, Licensing and Responsible Use

### Dataset Licence

The source dataset is identified as:

**Creative Commons Attribution 4.0 International - CC BY 4.0**

Appropriate attribution should be preserved when the dataset is reused.

### Model Licence

The Roboflow model records for both M1 and M2 display:

**AGPL-3.0**

Any future reuse, modification, distribution or deployment should consider the applicable licence terms and dependencies.

### External Images

External challenge images were used for qualitative evaluation only.

Image sources and applicable usage conditions should be retained. Images should not be redistributed where redistribution rights are uncertain.

### Privacy and Data Minimisation

A real construction-site implementation may capture identifiable workers.

A production deployment would therefore require appropriate consideration of:

- lawful purpose and authority;
- worker notification and applicable consent or other lawful basis;
- privacy and surveillance requirements;
- access control;
- secure storage;
- retention periods;
- restrictions on secondary use; and
- data minimisation.

The model is intended to evaluate PPE-related visual conditions rather than worker identity.

Facial recognition, identity inference and worker profiling are outside the scope of this project.

### Human Oversight

The model should be treated as a **decision-support tool**, not as an autonomous safety authority.

It should not independently be used to:

- determine disciplinary action;
- establish legal responsibility;
- identify individual workers;
- replace required safety inspections; or
- declare a workplace compliant or non-compliant without appropriate human review and contextual information.

Detailed governance analysis is provided in:

`docs/governance_and_licensing.md`

---

## Limitations

Important limitations include:

1. Strong held-out performance does not establish equivalent real-world performance.
2. External challenge testing was qualitative and used a small number of images.
3. The external images were not manually annotated as a formal ground-truth benchmark.
4. M2 was not subjected to the same external challenge-test protocol as M1.
5. Three defensible false-positive external examples were not preserved.
6. Ten individual validation-prediction screenshots were not subsequently generated.
7. Some historical hosted-training parameters were not independently preserved and have not been guessed.
8. The ontology does not include Non-Vest, Non-Gloves or Non-Shoes classes.
9. Absence of a positive PPE detection is not automatically proof of non-compliance.
10. Performance may vary across construction environments, camera positions, worker distances, lighting, PPE designs, occlusion and image quality.

These limitations are intentionally documented rather than hidden or reconstructed.

---

## Repository Structure

```text
construction-ppe-safety-detection-MAICEN-0526-Group-5/
|
|-- README.md
|-- requirements.txt
|-- .gitignore
|
|-- notebooks/
|   |-- MAICEN_0526_Group_5_Construction_PPE_Safety_Detection_Reproducibility_v0_2.ipynb
|   `-- maicen_0526_group_5_construction_ppe_safety_detection_reproducibility_v0_2.py
|
|-- docs/
|   |-- error_analysis.md
|   |-- governance_and_licensing.md
|   `-- reproducibility_notes.md
|
`-- results/
    |-- M1 model performance evidence
    |-- M2 model performance evidence
    |-- M1 training-curve evidence
    |-- M2 training-curve evidence
    |-- M1 baseline configuration evidence
    |-- M2 targeted-augmentation configuration evidence
    |-- 3 annotation examples
    `-- 5 external challenge-test prediction examples
```

The complete exported YOLO11 dataset archive is retained separately within the submission evidence package rather than committed to GitHub because of its size.

---

## Supporting Documentation

For detailed analysis, refer to:

- [`docs/error_analysis.md`](docs/error_analysis.md) - false-negative analysis, evidence limitations and prioritised iteration plan
- [`docs/governance_and_licensing.md`](docs/governance_and_licensing.md) - dataset/model licensing, privacy, safety, bias, data minimisation and human oversight
- [`docs/reproducibility_notes.md`](docs/reproducibility_notes.md) - dataset versions, experimental configurations, environment, reproducibility boundary and reproduction procedure
- [`notebooks/MAICEN_0526_Group_5_Construction_PPE_Safety_Detection_Reproducibility_v0_2.ipynb`](notebooks/MAICEN_0526_Group_5_Construction_PPE_Safety_Detection_Reproducibility_v0_2.ipynb) - executable Google Colab reproducibility notebook

---

## Final Experimental Position

The project demonstrates that YOLO11 Nano can achieve strong held-out performance on the selected construction-site PPE dataset while still exhibiting important generalisation weaknesses on external imagery.

The M1-to-M2 experiment also demonstrates why iterative model development should not be judged using a single headline metric. Targeted augmentation increased recall and improved held-out bare-arms performance, but other metrics decreased.

The principal technical lesson is therefore that **high internal evaluation performance must be considered together with external challenge testing, error analysis, ontology design, reproducibility and governance**.

For a safety-related AECO application, the appropriate role of the model is to support qualified human review rather than replace human safety judgement.
