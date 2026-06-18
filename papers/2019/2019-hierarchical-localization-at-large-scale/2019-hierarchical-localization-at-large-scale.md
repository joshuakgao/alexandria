---
title: "From Coarse to Fine: Robust Hierarchical Localization at Large Scale"
authors: [Paul-Edouard Sarlin, Cesar Cadena, Roland Siegwart, Marcin Dymczyk]
year: 2019
tags: [visual-localization]
url: ""
pdf: "[[2019-hierarchical-localization-at-large-scale.pdf]]"
date_ingested: 2026-06-18
---

# From Coarse to Fine: Robust Hierarchical Localization at Large Scale

![[2019-hierarchical-localization-at-large-scale-thumbnail.png]]

## Research gap

This paper introduces HF-Net (Hierarchical Feature Network), a CNN-based system for robust 6-DoF visual localization that combines global image descriptors and learned local features in a coarse-to-fine pipeline. The key innovation is a monolithic neural network that jointly predicts both global and local features, enabling efficient computation sharing. A two-stage localization process first retrieves candidate locations via global descriptor matching, then refines the pose estimate using local feature correspondence. Multitask distillation compresses multiple specialized models into a single compact network suitable for mobile deployment. HF-Net achieves state-of-the-art on large-scale localization benchmarks while maintaining real-time performance, with exceptional robustness to dramatic appearance changes (day/night, seasonal variations).

## Contributions

- **Hierarchical localization paradigm**: Efficient coarse-to-fine matching using global retrieval followed by local refinement
- **HF-Net architecture**: First CNN to jointly predict global and local features in a monolithic network, enabling computation sharing
- **Multitask distillation**: Novel training approach that compresses heterogeneous specialized predictors into one efficient model
- **Robustness to appearance changes**: Demonstrates exceptional performance under challenging conditions (day/night, seasonal variations, weather)
- **Mobile deployment**: Real-time localization on resource-constrained devices while maintaining accuracy and robustness

## Method

HF-Net combines global and local feature learning in a hierarchical pipeline:

**Global Retrieval (Coarse)**:
- Global descriptors match query image against database images using k-nearest neighbors
- Identifies candidate locations (prior frames) from sparse 3D model
- Computationally efficient due to far fewer database images than 3D points

**Local Matching (Fine)**:
- Learned local features (keypoints + descriptors) match between query and retrieved candidates
- CNN-based keypoint detection outperforms classical detectors (e.g., SuperPoint, DELF inspiration)
- Sparse learned features are fast to compute and suitable for mobile platforms

**Joint Prediction**:
- Single HF-Net produces both global and local features
- Shared computation backbone reduces model size and runtime
- Multitask distillation trains from multiple teacher models (global descriptor predictor, local feature detector)

**Pose Estimation**:
- 2D-3D correspondences from local matches + camera geometry → 6-DoF pose via PnP/RANSAC
- Integration with SfM reconstruction pipeline

## Datasets & evaluation

- **State-of-the-art on large-scale benchmarks**: Highest accuracy on localization challenges
- **Robustness in challenging conditions**: Superior performance under day/night, seasonal, and weather variations
- **Efficiency**: Real-time operation on mobile devices
- **Compact model**: Distillation enables small network size while maintaining accuracy
- **Generalization**: Robust across diverse environments (indoor, outdoor, urban, rural)

## Limitations

- How does performance scale to extremely large-scale environments (city-scale, beyond current benchmarks)?
- Can the hierarchical approach be extended to sequential/video-based localization with temporal consistency?
- How sensitive is performance to the quality and coverage of the reference 3D model?
- What are the failure modes in extreme appearance changes or novel environments?
- Can global + local feature learning be further optimized through end-to-end joint training rather than multitask distillation?

## Key takeaways

This work advances hierarchical localization (prior work: [42]) by leveraging recent progress in learned sparse features (SuperPoint [14], DELF [36]) and multi-task learning. It bridges the gap between robust but computationally expensive direct matching and efficient but less accurate image retrieval. The multitask distillation approach is novel in the context of localization and could be broadly applicable to other perception tasks requiring heterogeneous predictions.
