---
title: "SAMURAI: Adapting Segment Anything Model for Zero-Shot Visual Tracking with Motion-Aware Memory"
authors: [Cheng-Yen Yang, Hsiang-Wei Huang, Wenhao Chai, Zhongyu Jiang, Jenq-Neng Hwang]
year: 2024
venue: ""
tags: [segmentation]
url: ""
pdf: "[[2024-samurai-motion-aware-tracking.pdf]]"
date_ingested: 2026-06-18
---

# SAMURAI: Adapting Segment Anything Model for Zero-Shot Visual Tracking with Motion-Aware Memory

![[2024-samurai-motion-aware-tracking-thumbnail.png]]

## Research gap

SAMURAI adapts SAM 2 for visual object tracking by addressing two critical failure modes: confusion in crowded scenes with similar objects and poor memory utilization during occlusions. The method introduces motion-aware mechanisms without requiring fine-tuning or retraining. By combining motion modeling with intelligent memory selection, SAMURAI achieves competitive or state-of-the-art performance across multiple visual tracking benchmarks while maintaining real-time inference efficiency.

## Contributions

- **Motion modeling system**: Uses Kalman filtering to predict object trajectories and guide mask selection, reducing errors in crowded scenes
- **Motion-aware memory selection**: Replaces SAM 2's fixed-window memory with a hybrid scoring mechanism that selectively retains relevant frames based on motion, affinity, and object confidence scores
- **Zero-shot performance**: Achieves state-of-the-art results on LaSOT, LaSOText, and GOT-10k without additional training
- **Minimal overhead**: Integrates seamlessly into SAM 2 with negligible computational cost

## Method

**Motion Modeling**: Uses a linear Kalman filter with state vector [x, y, w, h, ẋ, ẏ, ẇ, ḣ] (position, size, and velocities). For each predicted mask, the IoU between the KF-predicted bounding box and mask is computed as a motion score (skf). The final mask is selected by weighted combination of motion score and original affinity score: M* = arg max(αkf · skf(Mi) + (1 - αkf) · smask(Mi)).

**Memory Selection**: Iterates backward from current frame, selecting frames where mask affinity, object confidence, and motion scores all meet thresholds. Constructs motion-aware memory bank: Bt = {mi | f(smask, sobj, skf) = 1, t - Nmax ≤ i < t}. This prevents error propagation by avoiding low-quality memory features during occlusions.

## Datasets & evaluation

- **LaSOT**: 74.2% AUC (SAMURAI-L), competitive with supervised methods
- **LaSOText**: 82.7% Pnorm, 7.1% improvement over SAM 2 baseline
- **GOT-10k**: 81.7% AO, 3.5% improvement over baseline
- **TrackingNet/NFS/OTB100**: Comparable or superior to state-of-the-art supervised trackers
- Particularly strong gains on fast motion (FM: +9.9%) and occlusion-related attributes (FOC: +13.2%, POC: +6.3%)

## Limitations

- Performance still lags supervised methods on some metrics despite zero-shot capability
- Motion modeling relies on successful initial tracking; robustness during extended occlusions unclear
- Hyperparameter sensitivity (αkf, thresholds) may require tuning for domain-specific applications
- Does not address failure modes beyond crowded scenes and occlusions (e.g., extreme scale changes)

## Key takeaways

Builds on [[2024-sam-segment-anything-video|SAM 2]], addressing its specific limitations in visual tracking. Complements traditional motion-based trackers (Kalman filter, Siamese networks) by integrating motion cues into a segmentation-based framework. Improves upon SAM2Long by using simpler motion-aware selection rather than tree-based memory.
