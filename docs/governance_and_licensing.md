# Governance and Licensing

## 1. Purpose

This document records the governance, licensing, privacy, safety and responsible-use considerations for the MAICEN-0526 Group 5 Construction-Site PPE Safety Detection project.

The project is an educational proof-of-concept investigating the use of YOLO11 object detection to identify construction-site workers and selected personal protective equipment (PPE) classes. It is not intended to replace qualified human safety inspection or to make autonomous safety-compliance decisions.

## 2. Dataset Licensing and Attribution

The source Construction Site Safety dataset was obtained through Roboflow Universe and is identified as licensed under Creative Commons Attribution 4.0 International (CC BY 4.0).

The project therefore preserves attribution to the original dataset source and records the dataset licence as part of the project evidence.

The experimental dataset contained 3,858 annotated source images across seven classes:

- Person
- Helmet
- Non-Helmet
- Vest
- Gloves
- Shoes
- bare-arms

The baseline split contained:

- 3,287 training images
- 340 validation images
- 231 test images

The original validation and test partitions were retained when the M2 targeted-augmentation experiment was created, supporting a controlled comparison between M1 and M2.

## 3. Model and Software Licensing

The Roboflow model records for both trained YOLO11 Nano models display the model licence as AGPL-3.0.

The two experimental models were:

- M1 baseline: `site-construction-safety-1uiqh-1-yolov11n-t1`
- M2 targeted augmentation: `site-construction-safety-1uiqh-3-yolov11n-t1`

The repository records this licensing information for transparency. Any future reuse, modification, distribution or deployment of the trained models or associated software must consider the applicable licence terms and any dependencies used by the implementation.

This academic repository should not be interpreted as granting additional rights beyond those provided by the respective dataset, software and model licences.

## 4. External Challenge Images

External images were used for qualitative challenge testing to investigate model behaviour outside the source dataset.

These images were not used as training data and were not treated as a formally annotated benchmark dataset.

External image sources and applicable usage conditions should be retained wherever practical. Images should not be redistributed through the repository where redistribution rights are uncertain. In such cases, source references and experimental observations should be retained instead.

## 5. Privacy and Consent

Computer-vision systems operating in construction environments may capture identifiable workers and other individuals.

A real deployment would therefore require consideration of:

- lawful authority and purpose for image collection;
- worker notification and appropriate consent or other lawful basis where required;
- restrictions on secondary use;
- secure storage and access controls;
- appropriate retention periods;
- applicable workplace, privacy and surveillance requirements; and
- procedures for handling privacy complaints or access requests.

The model should be designed to evaluate PPE-related visual conditions rather than worker identity.

Facial recognition, identity inference and worker profiling are outside the scope of this project.

## 6. Data Minimisation

A production implementation should collect and retain only the information necessary for the defined safety purpose.

Where technically practical, processing should occur without permanently retaining identifiable imagery.

Possible controls include:

- limiting camera coverage to relevant work areas;
- avoiding unnecessary capture of public or private spaces;
- restricting image retention;
- limiting access to authorised personnel;
- retaining safety events rather than continuous identifiable footage where appropriate; and
- using de-identification or privacy-preserving processing where feasible.

## 7. Model Limitations

Strong held-out dataset performance does not establish reliable performance in every real construction environment.

M1 achieved:

- mAP50: 96.93%
- mAP50-95: 80.63%
- Precision: 97.1%
- Recall: 94.2%

M2 achieved:

- mAP50: 96.69%
- mAP50-95: 78.58%
- Precision: 95.7%
- Recall: 94.8%

External challenge testing identified generalisation weaknesses under conditions including:

- domain shift;
- small or distant workers;
- partial visibility and cropping;
- occlusion;
- varied camera viewpoints;
- different lighting and appearance conditions; and
- challenging PPE or non-compliance examples.

Accordingly, the approximately 97% held-out mAP50 result must not be described as approximately 97% real-world safety accuracy.

## 8. False-Negative and False-Positive Risk

False negatives are particularly important in a safety application.

A false negative may result in a relevant worker, PPE item or non-compliance condition not being detected. If an automated system relied on that result without human review, a genuine safety concern could remain unflagged.

False positives also create operational risk because unnecessary alerts can increase review workload and potentially reduce confidence in the system.

The desired operating threshold therefore depends on the intended deployment context and the relative consequences of missed hazards and unnecessary alerts.

## 9. Ontology Limitation

The current seven-class ontology contains:

`Person`, `Helmet`, `Non-Helmet`, `Vest`, `Gloves`, `Shoes` and `bare-arms`.

It does not contain explicit:

- Non-Vest
- Non-Gloves
- Non-Shoes

classes.

Therefore, the absence of a Vest, Gloves or Shoes detection must not automatically be interpreted as evidence that the worker is violating a PPE requirement.

A production compliance system would require additional logic, potentially including:

1. worker detection;
2. PPE detection;
3. association of PPE items with the correct worker;
4. explicit compliance rules;
5. confidence and uncertainty handling; and
6. human review or escalation.

## 10. Bias and Representation

Model performance may vary according to conditions represented inadequately in the training data.

Relevant factors may include:

- construction-site type;
- camera position and viewing angle;
- worker distance;
- lighting and weather;
- occlusion;
- PPE colour and design;
- clothing appearance;
- image resolution; and
- regional work practices.

Future dataset development should evaluate coverage across relevant operating conditions rather than relying only on aggregate performance metrics.

## 11. Human Oversight and Responsible Use

The model should be treated as a safety decision-support tool, not as an autonomous authority.

Predictions should assist appropriately trained personnel in identifying observations that may require review.

The system should not independently be used to:

- determine disciplinary action;
- establish legal responsibility;
- infer worker intent;
- identify individuals;
- replace required safety inspections; or
- declare a workplace compliant or non-compliant without appropriate human review and contextual information.

## 12. Governance Checklist

| Governance Item | Project Position |
|---|---|
| Dataset source recorded | Yes |
| Dataset licence recorded | Yes - CC BY 4.0 |
| Model licence recorded | Yes - AGPL-3.0 |
| Dataset version/split documented | Yes |
| External images separated from training data | Yes |
| External testing identified as qualitative | Yes |
| Privacy considerations documented | Yes |
| Data minimisation considered | Yes |
| False-negative risk documented | Yes |
| False-positive risk documented | Yes |
| Model limitations documented | Yes |
| Ontology limitations documented | Yes |
| Bias/representation considerations documented | Yes |
| Human oversight required | Yes |
| Autonomous safety determination recommended | No |
| Real-world accuracy claimed from held-out mAP | No |

## 13. Responsible Deployment Position

The experimental results demonstrate that object detection can support construction-site PPE monitoring, but they also demonstrate why strong held-out metrics alone are insufficient for operational deployment.

Before real-world deployment, the system would require a representative and independently annotated external evaluation dataset, additional field testing, appropriate privacy and governance controls, clear operating procedures, monitoring for performance drift, and defined human-review responsibilities.

The appropriate role of the model is therefore to support human safety decision-making while preserving human accountability for final safety assessments.
