# Prioritizing Fundus Images for Glaucoma Review

**Joaquín Fontanella · Health AI course project · Bioengineering, UIC Barcelona**

An academic project using Python, PyTorch, and EfficientNet-V2-S to explore whether a model can help decide which fundus images to review first for signs of glaucoma. In the proposed workflow, every image would still receive professional review.

## Key finding

On 731 external images from the Hillel Yaffe Glaucoma Dataset (HYGD), the model achieved an **AUROC of 0.853**. At the threshold selected during internal validation, it prioritized **76 of 536 reference-positive images (14.2%)**. The other 460 reference-positive images stayed in the standard review queue.

The AUROC measures how well the model ranks positive images above negative ones across thresholds. The 14.2% measures how many positives actually crossed the chosen threshold. A positive image can score higher than many negative images and still fall below the cutoff.

## What I did

1. Used ResNet-50 and ORIGA to establish a baseline.
2. Corrected label mappings, rebuilt the data splits, and trained an EfficientNet-V2-S model in PyTorch.
3. Analyzed results by source. EyePACS accounted for **300 of the 315 positive images prioritized** in the internal test. The internal test AUROC was **0.947** across all sources and **0.842** after excluding EyePACS.
4. Audited the external images for duplicates and possible overlap with the development data. After removing 16 images during the duplicate audit, I evaluated the remaining **731 images from 284 patient identifiers** using the original model weights and threshold.

## External results

The threshold was **0.897**, rounded here for readability. It was selected on the internal validation split and kept fixed for HYGD.

| HYGD reference label | Priority review | Standard review | Total |
| --- | ---: | ---: | ---: |
| Positive | 76 | 460 | 536 |
| Negative | 0 | 195 | 195 |
| **Total** | **76** | **655** | **731** |

| Metric | Result |
| --- | ---: |
| AUROC | 0.853 (95% CI 0.814–0.889) |
| Sensitivity at the fixed threshold | 14.2% (76/536) |
| Observed specificity at the fixed threshold | 100% (195/195) |
| Images assigned priority | 10.4% (76/731) |

The AUROC confidence interval was estimated by resampling at the patient identifier level.

<!-- After uploading the histogram, uncomment the line below and check the file path.
![HYGD model score distributions by reference label, with the original threshold marked](figures/hygd-score-distribution.png)
-->

## Limitations

- **Reference labels.** “Positive” refers to the dataset annotation. This project evaluated image prioritization for professional review.
- **Cohort composition.** In the evaluated HYGD subset, 73.3% of images were reference-positive. The composition of a screening population could differ substantially.
- **Patient overlap.** The image audit detected no overlap between HYGD and the development images. Patient identity across data sources could not be verified, and patient-level separation within the development data was incomplete.
- **Clinical workflow.** The evaluation measured which images would move into the priority queue. Effects on review times and patient outcomes remain untested.

## External dataset

[Hillel Yaffe Glaucoma Dataset (HYGD), version 1.1.0](https://doi.org/10.13026/m92s-0z95), available through PhysioNet.
