---
title: "Jersey Number Detection Using Synthetic Data in a Low-Data Regime"
authors: [Divya Bhargavi, Sia Gholami, Erika Pelaez Coyotl]
year: 2022
venue: "Frontiers in Artificial Intelligence"
tags: [sports-analytics]
url: "https://doi.org/10.3389/frai.2022.988113"
date_ingested: 2026-08-23
---

# Jersey Number Detection Using Synthetic Data in a Low-Data Regime

![[2022-jersey-number-detection-synthetic-data-thumbnail.png]]

## Research gap
Existing jersey number recognition methods require large annotated datasets, custom bounding box labels for number regions, and/or large complex models with tens of millions of parameters. These requirements are cost-prohibitive for most sports organizations, particularly those working with low-resolution practice footage where jersey numbers occupy fewer than 32×32 pixels.

## Contributions
- A novel two-stage synthetic data generation pipeline (Simple2D and Complex2D) that creates double-digit number images with increasing visual complexity, used via curriculum learning to pre-train CNN classifiers.
- A three-step jersey number detection pipeline that chains pre-trained person detection (CenterNet) and pose estimation (AlphaPose) models to localize jersey number regions without requiring custom bounding box annotations.
- Dual classification strategy combining multi-class (101 classes: 0–99 + unrecognizable) and multi-label (digit-wise, 21 classes) models into an ensemble, achieving 89.3% accuracy on a highly imbalanced American football dataset.
- Demonstration that synthetic data pre-training improves overall accuracy by 9% and accuracy on low-frequency numbers by 18%.

## Method
The pipeline operates in three stages:

1. **Person detection**: A pre-trained CenterNet (ResNet50 backbone) detects and crops players from video frames sampled at 5 fps from 1280×720 practice footage.
2. **Jersey number localization**: A pre-trained AlphaPose model identifies four torso keypoints (left/right shoulders and hips) on each cropped player. The bounding box defined by these keypoints is expanded 60% outward to capture the full jersey number region, producing torso crops averaging 20×25 pixels.
3. **Number classification**: Two CNN classifiers (ResNet50 backbone) are trained:
   - **Multi-class**: 101-class softmax over numbers 0–99 plus an unrecognizable class.
   - **Multi-label**: 21-class sigmoid predicting left digit (0–9), right digit (0–9), and unrecognizable, enabling compositional generalization to unseen number combinations.

Synthetic data generation follows a curriculum:
- **Simple2D**: 400,000 images (4,000 per class) of two-digit numbers rendered with team-specific fonts and jersey colors on solid backgrounds, with three tiers of augmentation (Gaussian noise, grid distortion, channel shuffling/rotation).
- **Complex2D**: 400,000 images where Simple2D numbers are superimposed on random COCO images, adding realistic background complexity.

Models are sequentially pre-trained on Simple2D → Complex2D → fine-tuned on real football data. The final prediction uses an ensemble of both models.

## Datasets & evaluation
- **Training data**: ~3,000 labeled torso crops from six Seattle Seahawks practice videos (two camera angles: endzone and sideline), with severe class imbalance (number 3 has 500+ images; numbers 43, 63, 69, 93 have ≤10).
- **Synthetic data**: 400,000 Simple2D + 400,000 Complex2D images.
- **Test data**: Four additional practice videos, with 20 images per number after upsampling.

| Configuration | Multi-class | Multi-label | Ensemble |
|---|---|---|---|
| Football only (baseline) | 80.6% | 80.0% | 80.3% |
| Simple2D + Football | 82.8% | 82.0% | 83.1% |
| Complex2D + Football | — | 88.0% | — |
| Simple2D + Complex2D + Football | 88.9% | 86.0% | **89.3%** |

The approach achieves comparable accuracy to methods with 10× more parameters (e.g., pose-guided R-CNN at 41.5M params vs. 4.1M here) and 4–12× more annotated data.

## Limitations
- Evaluation is limited to a single team's practice footage; cross-team and cross-sport generalization is not tested.
- The cascading three-model pipeline introduces compounding errors from person detection and pose estimation stages.
- No public benchmark dataset exists for jersey number detection, making cross-method comparison difficult — results from prior work are self-reported on different private datasets.
- Image quality is the primary bottleneck; the very small torso crops (~20×25 pixels) limit achievable accuracy regardless of model sophistication.
- Only ResNet50 was evaluated as the backbone; larger or more modern architectures were not explored.

## Key takeaways
- Simple synthetic data generation (rendering digits with team-specific fonts/colors on solid and natural backgrounds) is surprisingly effective for bootstrapping jersey number classifiers in low-data regimes, providing a 9% absolute accuracy improvement.
- Using pre-trained pose estimation for jersey localization eliminates the need for custom bounding box annotations, dramatically reducing labeling cost — only number class labels are needed.
- The multi-label (digit-wise) formulation enables compositional generalization: even if number 73 is unseen in training, the model has seen digits 7 and 3 in other positions, enabling reasonable predictions.
- Curriculum learning from simple to complex synthetic data outperforms training on either synthetic dataset alone.
