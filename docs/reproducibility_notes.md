# Reproducibility Notes

## 1. Purpose

This document defines the reproducibility boundary, experimental configuration and execution requirements for the MAICEN-0526 Group 5 Construction-Site PPE Safety Detection project.

The repository is designed so that the project methodology, recorded experimental results and comparison calculations can be reviewed and reproduced through Google Colab without requiring a local software installation.

The historical Roboflow-hosted training jobs are not rerun by the reproducibility notebook. Instead, the notebook records the original experimental configurations and results and provides executable verification and comparison steps.

## 2. Source Dataset

The project used a Construction Site Safety object-detection dataset obtained through Roboflow Universe.

Source dataset characteristics:

- 3,858 annotated images
- 7 object-detection classes
- 3,287 training images
- 340 validation images
- 231 test images
- Image size: 640 x 640
- Preprocessing: Resize using Fit with black-edge padding
- Source dataset licence: CC BY 4.0

Classes:

1. Person
2. Helmet
3. Non-Helmet
4. Vest
5. Gloves
6. Shoes
7. bare-arms

The validation and test partitions were retained between the M1 and M2 experiments to support a controlled comparison.

## 3. M1 Baseline Configuration

M1 was the baseline experiment.

Configuration:

- Architecture: YOLO11 Nano object detection
- Dataset: Roboflow Version 1
- Starting checkpoint: MS COCO
- Input size: 640 x 640
- Preprocessing: Fit with black-edge padding
- Training augmentation: None added to the baseline dataset
- Roboflow-hosted training
- Approximate hosted training duration: 1 hour

Model identifier:

`site-construction-safety-1uiqh-1-yolov11n-t1`

The final training graph recorded Epoch 299.

### M1 Recorded Results

| Metric | M1 |
|---|---:|
| mAP50 | 96.93% |
| mAP50-95 | 80.63% |
| Precision | 97.1% |
| Recall | 94.2% |
| F1 | 95.6% |

## 4. M2 Targeted-Augmentation Configuration

M2 was designed after reviewing M1 external challenge-test behaviour.

The objective was to investigate whether targeted augmentation could improve robustness to conditions such as partial framing, viewpoint variation, orientation and lighting changes while preserving strong held-out performance.

M2 configuration:

- Architecture: YOLO11 Nano object detection
- Dataset: M2 Targeted Augmentation - 2x
- Starting checkpoint: MS COCO
- Input size: 640 x 640
- Preprocessing: Fit with black-edge padding
- Augmentation multiplier: 2x
- Total generated dataset size: 7,145 images
- Training images after augmentation: 6,574
- Validation images: 340
- Test images: 231
- Roboflow-hosted training
- Approximate hosted training duration: 6 hours

Targeted augmentations:

- Horizontal Flip
- Random Crop: 0% to 10%
- Rotation: -15 degrees to +15 degrees
- Brightness: -15% to +15%

Model identifier:

`site-construction-safety-1uiqh-3-yolov11n-t1`

The final training graph recorded Epoch 264.

### M2 Recorded Results

| Metric | M2 |
|---|---:|
| mAP50 | 96.69% |
| mAP50-95 | 78.58% |
| Precision | 95.7% |
| Recall | 94.8% |
| F1 | 95.2% |

## 5. Controlled M1 versus M2 Comparison

| Metric | M1 | M2 | Change |
|---|---:|---:|---:|
| mAP50 | 96.93% | 96.69% | -0.24 percentage points |
| mAP50-95 | 80.63% | 78.58% | -2.05 percentage points |
| Precision | 97.1% | 95.7% | -1.4 percentage points |
| Recall | 94.2% | 94.8% | +0.6 percentage points |
| F1 | 95.6% | 95.2% | -0.4 percentage points |

Selected class-level held-out test mAP50 results included:

| Class | M1 | M2 | Change |
|---|---:|---:|---:|
| Gloves | 98% | 97% | -1 pp |
| Helmet | 99% | 99% | 0 pp |
| Non-Helmet | 97% | 96% | -1 pp |
| Person | 99% | 99% | 0 pp |
| Shoes | 97% | 97% | 0 pp |
| Vest | 99% | 99% | 0 pp |
| bare-arms | 91% | 93% | +2 pp |

M2 therefore represents a trade-off rather than a universal performance improvement. Recall and bare-arms test performance improved, while several other metrics decreased.

M2 was not subsequently evaluated using the same external challenge-test protocol. No claim is therefore made that M2 improved real-world generalisation.

## 6. External Challenge-Test Protocol

M1 was additionally evaluated qualitatively using external images that were not part of the source dataset.

The testing protocol used:

- confidence threshold: 50%
- overlap threshold: 50%
- opacity: 75%
- original external images without deliberate cropping or modification
- consistent thresholds across tests

The external images were not manually annotated as a formal ground-truth dataset. The external results are therefore presented as qualitative challenge-test evidence rather than formal mAP measurements.

The testing identified weaknesses including missed Non-Helmet, bare-arms and Gloves conditions, as well as difficulty with some distant, background, partially visible and out-of-distribution workers.

## 7. Google Colab Reproducibility

The primary reproducibility notebook is:

`MAICEN_0526_Group_5_Construction_PPE_Safety_Detection_Reproducibility_v0_2.ipynb`

A corresponding Python representation is also provided:

`maicen_0526_group_5_construction_ppe_safety_detection_reproducibility_v0_2.py`

The notebook was successfully executed using a restart-and-run-all workflow.

The verified execution environment recorded:

- Python: 3.13.15
- Operating environment: Linux x86_64
- Ultralytics: 8.4.156
- Roboflow: 1.5.0

The notebook includes installation of required packages using:

`pip install ultralytics roboflow`

The repository also contains `requirements.txt` for dependency documentation.

## 8. Reproducibility Boundary

Two different forms of reproducibility are distinguished.

### Executable reproducibility

The supplied notebook can be opened in Google Colab and executed to:

- verify the software environment;
- inspect the recorded dataset and experiment configuration;
- reproduce metric comparisons;
- reproduce the documented analytical interpretation; and
- review the governance and experimental limitations.

### Historical experiment reproducibility

M1 and M2 were trained previously using Roboflow-hosted training.

The notebook does not claim to recreate those historical training jobs from scratch. Instead, the original model identifiers, dataset versions, configurations, training curves and recorded metrics are preserved as experimental evidence.

This distinction prevents newly generated notebook calculations from being incorrectly represented as newly reproduced model-training results.

## 9. Training Hyperparameter Limitation

The experiments used Roboflow-hosted training and the selected/default training configuration available through that workflow.

Where a specific historical parameter was not preserved in the available experimental evidence, it is not retrospectively invented.

The final observed training epochs are documented from the preserved training curves, but unspecified historical parameters should not be interpreted as independently verified configuration values.

## 10. Dataset Export and Large Files

A YOLO11-format export of the baseline dataset has been retained in the project evidence package.

The exported dataset preserves the training, validation and test directory structure and provides additional reproducibility evidence.

The complete dataset archive is intentionally not committed to this GitHub repository because of its size and because dataset distribution must remain consistent with the applicable source licence and attribution requirements.

The repository instead documents the dataset source, version, split, preprocessing and experimental configuration.

## 11. Reproduction Procedure

To review and reproduce the executable analytical workflow:

1. Open the repository in GitHub.
2. Navigate to the `notebooks` directory.
3. Open `MAICEN_0526_Group_5_Construction_PPE_Safety_Detection_Reproducibility_v0_2.ipynb`.
4. Open or upload the notebook in Google Colab.
5. Use a fresh Colab runtime.
6. Run the notebook from the beginning using Run All.
7. Allow the package-installation cell to complete.
8. Restart the runtime if requested after package installation.
9. Run all cells again.
10. Confirm the environment-verification output.
11. Review the recorded M1 and M2 configurations.
12. Run the metric-comparison cells.
13. Review the error-analysis, governance and reproducibility limitations.

The expected notebook execution time is substantially shorter than the historical hosted model-training times because the notebook verifies and analyses the preserved experiments rather than retraining M1 and M2.

## 12. Evidence Integrity

The project follows the principle that missing experimental evidence should be disclosed rather than reconstructed or fabricated.

Accordingly:

- historical model metrics are identified as recorded Roboflow results;
- external challenge testing is identified as qualitative;
- M2 external generalisation is not claimed because equivalent M2 external testing was not completed;
- three defensible false-positive examples were not retrospectively invented;
- unavailable individual validation-prediction screenshots are recorded as an evidence limitation; and
- unspecified historical training parameters are not guessed.

This boundary is intended to make the repository transparent, auditable and reproducible within the evidence actually preserved during the experiment.
