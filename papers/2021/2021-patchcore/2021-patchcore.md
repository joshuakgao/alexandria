---
title: "Towards Total Recall in Industrial Anomaly Detection"
authors: [Karsten Roth, Latha Pemula, Joaquin Zepeda, Thomas Brox, Peter Gehler]
year: 2021
venue: "CVPR 2022"
tags: [anomaly-detection]
url: ""
pdf: "[[2021-patchcore.pdf]]"
date_ingested: 2026-06-18
---

# Towards Total Recall in Industrial Anomaly Detection

![[2021-patchcore-thumbnail.png]]

## Research gap

PatchCore addresses the cold-start anomaly detection problem in industrial visual inspection—detecting defects using only nominal (non-defective) examples. Rather than adapting pretrained ImageNet models to the target domain, the paper leverages mid-level patch-level features from frozen pretrained networks combined with a memory bank of nominal features. A key innovation is locally-aware patch aggregation, which increases the receptive field and spatial context without losing resolution or introducing ImageNet bias. To make the approach practical at scale, the authors introduce greedy coreset subsampling to reduce memory requirements and inference time while preserving feature coverage. The method achieves nearly perfect performance on MVTec AD (99.6% AUROC), more than halving the error of prior work, while maintaining competitive inference speeds and strong spatial localization of defects.

## Contributions

- **Locally-aware patch features:** Aggregates mid-level network features over neighborhoods to balance receptive field size, spatial resolution, and robustness to ImageNet bias
- **Memory bank design:** Builds a maximally representative store of nominal patch-features that can be efficiently searched at test time
- **Greedy coreset subsampling:** Applies minimax facility location coreset selection to reduce the memory bank by 10–100× while maintaining feature coverage (using Johnson-Lindenstrauss projection for speed)
- **State-of-the-art industrial results:** 99.6% image-level AUROC on MVTec AD and strong pixel-level localization, achieving near-perfect performance with minimal adaptation

## Method

**Feature extraction:** Uses a pretrained backbone (e.g., ResNet50) and extracts features at intermediate levels (j ∈ {2,3}) rather than the final layer. For each spatial position in the feature map, the method aggregates over a local neighborhood of patch features using adaptive average pooling (Equation 2), creating locally-aware patch representations that retain spatial context while reducing ImageNet-specific bias.

**Memory bank construction:** All nominal training samples are processed to extract patch-level features at stride s (typically 1). These patches are collected into a memory bank M (Equation 4).

**Coreset reduction:** To handle large image counts and high-resolution feature maps, applies iterative greedy minimax coreset selection (Equation 5, Algorithm 1) to find a representative subset MC of M. Uses random projection to reduce dimensionality for faster selection.

**Anomaly scoring at test time:** For each test image, computes patch-level features and finds the maximum distance to the nearest neighbor in MC (Equation 6). Re-weights by the local density of neighbors (Equation 7) to increase scores when anomalies occur in rare regions of feature space. Image-level score is the maximum patch-level score; segmentation maps are derived by realigning patch scores.

## Datasets & evaluation

**MVTec AD (15 diverse industrial products):**
- PatchCore–25% (1/4 coreset reduction): 99.1% AUROC, halving the 4.7% error of PaDiM
- PatchCore–10%: 99.0% AUROC
- PatchCore–1% (100× reduction): 99.0% AUROC with negligible performance loss
- Pixel-level AUROC: 98.1% (vs. PaDiM 97.5%)
- Competitive inference times maintained across all subsampling levels

**Magnetic Tile Defects (MTD):** Achieves competitive results on a more specialized industrial dataset.

**Mini Shanghai Tech Campus (mSTC):** Extends to non-industrial anomaly localization with solid performance, showing generalization beyond manufacturing.

**Few-shot regime:** Demonstrates sample efficiency—matches or exceeds baselines while using only a fraction of training data.

## Limitations

- **Generalization beyond MVTec:** While mSTC and MTD results are strong, the approach has primarily been evaluated on structured industrial inspection tasks with clear lighting and controlled setups
- **Feature hierarchy selection:** The choice of which intermediate layers to use (j ∈ {2,3}) is dataset-dependent; a systematic way to select layers could strengthen the approach
- **Coreset target selection:** The choice of coreset reduction percentage (1%, 10%, 25%) involves a performance–efficiency trade-off but no principled guidance is provided
- **Local smoothing assumptions:** The adaptive average pooling for neighborhood aggregation may not be optimal for all anomaly types (e.g., very fine textures vs. large structural defects)

## Key takeaways

Builds directly on [[2021-reconstruction-by-inpainting-anomaly-detection|feature-based one-class classification]] approaches but improves over SPADE and PaDiM by:
- Using patch-level rather than layer-level features, capturing finer spatial details
- Incorporating local neighborhood aggregation to reduce ImageNet bias without sacrificing resolution
- Applying coreset subsampling (inspired by greedy facility location in classical ML) to scale memory bank lookup
- Achieving both better detection accuracy and faster inference than prior work despite using richer nominal context
