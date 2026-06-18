---
title: "FE2E: From Editor to Dense Geometry Estimator"
authors: [Jiyuan Wang, Chunyu Lin, Lei Sun]
year: 2026
tags: [depth-estimation, diffusion-models]
url: ""
pdf: "[[2026-fe2e-from-editor-to-dense-geometry-estimator.pdf]]"
date_ingested: 2026-06-18
---

# FE2E: From Editor to Dense Geometry Estimator

![[2026-fe2e-from-editor-to-dense-geometry-estimator-thumbnail.png]]

## Research gap

This paper demonstrates that image editing models (specifically DiT-based editors like Step1X-Edit) are better suited foundations for dense geometry prediction than text-to-image generators like Stable Diffusion. FE2E (From Editor to Estimator) adapts a state-of-the-art editor for monocular depth and normal estimation through careful architectural and training modifications. The key insight is that editing models inherently possess structural priors aligned with geometric understanding, requiring only "refinement" rather than fundamental feature reshaping. FE2E achieves exceptional zero-shot performance with limited training data: 35% AbsRel improvement on ETH3D, outperforming DepthAnything series despite using 100× less training data. When trained on only 0.2% of geometric ground truth, FE2E still exceeds fully-supervised data-driven baselines.

## Contributions

- **Paradigm shift**: First work to systematically adapt a pre-trained image editing model (rather than T2I generator) for dense geometry prediction
- **Architectural insights**: Demonstrate that editing models possess inherent structural/geometric priors that accelerate convergence and improve final performance
- **Technical solutions**: (1) Reformulate flow matching loss to "consistent velocity" objective for deterministic prediction; (2) Logarithmic quantization to resolve BFloat16/FP32 precision conflicts; (3) Cost-free joint depth+normal estimation via parallel outputs
- **Empirical superiority**: Dramatic improvements with minimal data (0.2% GT data training), suggesting editing models better capture geometric structure than conventional dense prediction architectures

## Method

FE2E adapts Step1X-Edit (a DiT-based editor fine-tuned from FLUX) through three key modifications:

**1. Training objective redesign**:
- Original: Flow matching with instantaneous velocity v(zt) = dz/dt
- New: "Consistent velocity" training with fixed starting point, better suited to deterministic prediction

**2. Precision handling**:
- Problem: Step1X-Edit trained in BFloat16, insufficient for depth/normal precision needs
- Solution: Logarithmic quantization strategy to reduce precision artifacts while maintaining computational efficiency

**3. Joint estimation efficiency**:
- Leverage DiT's parallel output design to simultaneously estimate depth and normals without additional inference cost

Architecture integrates: mask representation codec → base editor (DiT) → logarithmic quantization → dual geometry outputs.

## Datasets & evaluation

**Zero-shot Depth Estimation:**
- ETH3D: 1.4 AbsRel (35% improvement over prior SoTA), ranked 1st
- Outperforms DepthAnything v1/v2 despite training on 280K vs. their multi-billion-token datasets
- Average ranking: 1.4 across multiple datasets (vs. DepthAnything's 3.5)

**Zero-shot Normal Estimation:**
- Average ranking: 1.67, top performer
- Consistent performance across diverse datasets (indoor/outdoor)

**Training efficiency:**
- Only 71K training samples used (vs. 280K+ for DepthAnything)
- Even 0.2% GT data training outperforms fully-supervised DepthAnything baselines

## Limitations

- How well does this approach generalize to other dense prediction tasks (segmentation, surface reconstruction)?
- Can the consistent velocity formulation be applied to other deterministic vision tasks?
- How do editing model architectural choices (attention patterns, feature resolution) impact geometric prediction quality?
- What is the minimal training data required to maintain SoTA performance across diverse test distributions?

## Key takeaways

This work builds on recent progress in leveraging pre-trained generative models for dense prediction (MiDaS, DPT, DepthAnything, zero-shot depth with Stable Diffusion). It advances the field by proposing that image editing models are fundamentally better suited than T2I generators for image-to-image tasks like depth/normal estimation. The work complements concurrent research on diffusion transformers and their application to perception tasks.
