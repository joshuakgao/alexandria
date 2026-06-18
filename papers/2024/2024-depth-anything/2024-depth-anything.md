---
title: "Depth Anything: Unleashing the Power of Large-Scale Unlabeled Data"
authors: [Lihe Yang, Bingyi Kang, Zilong Huang, Xiaogang Xu, Jiashi Feng, Hengshuang Zhao]
year: 2024
tags: [depth-estimation]
url: ""
pdf: "[[2024-depth-anything.pdf]]"
date_ingested: 2026-06-18
---

# Depth Anything: Unleashing the Power of Large-Scale Unlabeled Data

![[2024-depth-anything-thumbnail.png]]

## Research gap

Depth Anything presents a foundation model for monocular depth estimation built on large-scale unlabeled data. The core contribution is demonstrating that leveraging 62M unlabeled images via self-training can substantially improve zero-shot generalization beyond traditional supervised approaches. Rather than pursuing architectural novelty, the work achieves strong results through careful data engineering and a two-stage training strategy: first training a teacher on 1.5M labeled images, then using it to pseudo-label and jointly train a student on unlabeled data. The method reveals a critical insight: naive self-training fails when sufficient labeled data exists, but adding strong data augmentations (color distortion and CutMix) forces the student to learn robust, invariant representations that generalize better. The work also proposes preserving semantic priors via feature alignment with DINOv2 rather than explicit semantic supervision.

## Contributions

- **Data engine for pseudo-labeling**: Demonstrated the feasibility of scaling depth estimation to 62M unlabeled images by automatically annotating with a pre-trained teacher model
- **Strong perturbations for self-training**: Showed that naive pseudo-labeled data provides minimal benefit when labeled data is sufficient; color distortion and CutMix force learning of robust representations
- **Semantic preservation via feature alignment**: Proposed aligning with frozen DINOv2 features instead of using auxiliary segmentation, preserving high-dimensional semantic information
- **Strong zero-shot generalization**: Outperforms MiDaS v3.1 significantly across six unseen datasets despite using fewer labeled datasets

## Method

### Self-training pipeline

The method consists of two stages:

1. **Teacher training** (20 epochs): Train teacher model T on 1.5M labeled images from 6 datasets using affine-invariant loss to handle scale/shift differences across datasets
2. **Pseudo-labeling**: Forward all 62M unlabeled images through teacher T to create pseudo-labels: D̂ᵤ = {(uᵢ, T(uᵢ)) | uᵢ ∈ Dᵤ}
3. **Student training**: Train student model S on combined labeled and pseudo-labeled data, re-initializing from scratch rather than fine-tuning

### Addressing naive self-training failure

Initial attempts with direct self-training failed to improve over labeled-only baseline. The authors conjecture this occurs because teacher and student share the same pre-training and architecture, causing similar predictions on unlabeled set even without explicit self-training.

**Solution: Strong perturbations** — inject data augmentations during student training only:
- **Color distortions**: Jittering and Gaussian blur
- **Spatial distortion**: CutMix (mixing random pairs of images)

For CutMix, given images uₐ and u_b and binary mask M:
- u_ab = uₐ ⊙ M + u_b ⊙ (1 - M)
- Compute losses separately for M and (1-M) regions
- Applied with 50% probability; teacher uses clean images for pseudo-labeling

These perturbations compel the student to seek extra visual knowledge and develop invariant representations.

### Semantic preservation

Instead of auxiliary segmentation (which discretizes information), preserve rich semantic priors via feature alignment loss:

L_feat = 1 - (1/HW) Σ cos(fᵢ, f'ᵢ)

where f is depth model feature and f' is frozen DINOv2 feature. Key refinement: tolerance margin α = 0.85 — pixels exceeding this cosine similarity threshold are excluded from loss. This allows discriminative depth representations while inheriting semantic understanding.

## Datasets & evaluation

**Zero-shot evaluation** (Table 2):
- ViT-L Depth Anything outperforms MiDaS ViT-L across all six datasets
- DDAD: AbsRel 0.251 → 0.230; δ₁ 0.766 → 0.789
- ViT-S model (24.8M params) outperforms MiDaS ViT-L (335.3M) on Sintel, DDAD, ETH3D
- Strong performance on diverse conditions: low-light, complex scenes, fog, remote distances

**Multi-task capability**: Encoder also achieves competitive semantic segmentation results despite no explicit segmentation training.

## Limitations

- Why do strong perturbations specifically enable unlabeled data leverage? Is the mechanism robust invariance learning or forcing the model to reason about deformations?
- Computational cost of running teacher forward pass on 62M images — feasible but how does this cost scale?
- Pseudo-label noise: CutMix mixes images with different depth distributions; does this introduce systematic bias?
- DINOv2 dependency: How sensitive is semantic preservation to the choice of frozen encoder?

## Key takeaways

- **vs. MiDaS**: Uses fewer labeled datasets but larger unlabeled scale; achieves better zero-shot generalization
- **vs. naive semi-supervised learning**: Distinguishes from literature assuming limited labels by showing that pseudo-labels require perturbation-based curriculum when sufficient labeled data exists
- **vs. auxiliary semantic tasks**: Previous works improve MDE via explicit semantic segmentation; this work shows feature-space alignment is superior for preserving semantic information while maintaining depth discriminability
