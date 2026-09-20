# Error Analysis

## 1. Purpose

This error analysis evaluates the limitations observed during controlled external challenge testing of the M1 baseline PPE object-detection model. External images were not part of the source dataset and were used to examine model behaviour under conditions that differed from the held-out validation and test data.

The external challenge testing was qualitative rather than a formal mAP evaluation because the external images were not manually annotated as a ground-truth evaluation dataset.

## 2. False-Negative Analysis

Three clear false-negative patterns were identified from the preserved external challenge evidence.

| Error | Observation | Error Type | Likely Cause | Proposed Improvement |
|---|---|---|---|---|
| FN-01 | A visible PPE/safety-related object or condition was not detected at the fixed 50% confidence threshold. | False Negative | Domain shift and appearance variation | Add more representative examples of difficult PPE and non-compliance conditions. |
| FN-02 | Relevant objects/persons in a complex scaffolding or construction scene were missed. | False Negative | Occlusion, small-object scale and scene complexity | Add small, partially occluded and varied-viewpoint examples and consider higher-resolution evaluation. |
| FN-03 | A distant/background person was not consistently detected. | False Negative | Small object size and distance from camera | Increase representation of distant workers and varied camera viewpoints in training data. |

Additional external testing performed during the experiment also identified weaknesses involving Non-Helmet, bare-arms and Gloves under challenging real-world image conditions.

## 3. False-Positive Evidence

Three defensible false-positive examples were not established in the preserved external challenge-test evidence.

No false-positive examples have been fabricated or retrospectively relabelled merely to satisfy an evidence count. This is recorded as a limitation of the experimental evidence. Future evaluation should use a manually annotated external test set so that false positives and false negatives can be identified systematically and quantified using a confusion matrix and class-level metrics.

## 4. M1 to M2 Iteration

M2 introduced targeted augmentation based on weaknesses observed during M1 external testing:

- horizontal flip;
- random crop from 0% to 10%;
- rotation from -15 degrees to +15 degrees; and
- brightness variation from -15% to +15%.

The M2 dataset used a 2x augmentation multiplier while retaining the original validation and test partitions.

M1 achieved 96.93% mAP50 and 80.63% mAP50-95. M2 achieved 96.69% mAP50 and 78.58% mAP50-95. M2 recall increased from 94.2% to 94.8%, while precision decreased from 97.1% to 95.7%. The bare-arms test-set mAP50 improved from 91% to 93%.

These results show a controlled trade-off rather than a universal improvement. M2 external challenge testing was not completed, so the experiment does not claim that M2 improved real-world generalisation.

## 5. Prioritised Data and Model Improvements

1. **Increase representative targeted training data** - collect and annotate more examples of difficult PPE and non-compliance conditions, especially Non-Helmet, bare-arms and Gloves across varied construction environments.

2. **Improve small-object, crop and viewpoint robustness** - increase examples of distant workers, partially visible workers, occlusion, unusual camera angles and close-up/cropped PPE.

3. **Improve the compliance ontology and rule layer** - the current ontology contains Person, Helmet, Non-Helmet, Vest, Gloves, Shoes and bare-arms, but does not contain explicit Non-Vest, Non-Gloves or Non-Shoes classes. Absence of a positive PPE detection must therefore not automatically be interpreted as a safety violation. A practical system should associate PPE with individual workers and apply an explicit compliance rule layer.

## 6. Safety Interpretation

False negatives are particularly important in a construction-safety application because missed non-compliance conditions could prevent a potential hazard from being flagged. The detector should therefore be treated as a decision-support tool rather than an authoritative safety determination.

Human review remains necessary, particularly for ambiguous, occluded, distant or out-of-distribution observations.
