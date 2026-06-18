---
title: "Non-parametric Memory for Spatio-Temporal Segmentation of Construction Zones for Self-Driving"
authors: [Shenlong Wang, Kelvin Wong, Ersin Yumer Raquel Urtasun]
year: 2021
venue: "ICRA"
tags: [autonomous-vehicles, construction-monitoring, 3d-scene-understanding, segmentation]
url: ""
pdf: "[[2021-non-parametric-memory-construction-zones.pdf]]"
date_ingested: 2026-06-18
---

# Non-parametric Memory for Spatio-Temporal Segmentation of Construction Zones for Self-Driving

![[2021-non-parametric-memory-construction-zones-thumbnail.png]]

## Research gap

This paper introduces a non-parametric memory representation for detecting and tracking construction zone elements (cones, barrels, signs) for autonomous vehicles. The core insight is that AV perception of small, safety-critical objects must aggregate information across multiple timesteps — reinforcing uncertain detections as the vehicle approaches, forgetting false positives when contradicted by new observations, and remembering occluded objects that are temporarily hidden. These three operations (remember/reinforce/forget) are learned rather than hand-coded, using 3D occlusion reasoning to distinguish between "not visible because occluded" and "not visible because it was a false positive."

## Contributions

- A non-parametric memory representation with three learned operations (remember, reinforce, forget) driven by 3D occlusion reasoning for spatio-temporal object segmentation
- Integration of camera-based segmentation with LiDAR-based 3D localization and occlusion computation for construction zone detection
- Learned aggregation (via continuous convolutions) replacing hand-coded Bayesian update rules for fusing past beliefs with new observations
- Demonstration on safety-critical AV perception: small construction elements at range that require temporal accumulation for reliable detection

## Method

**2D Segmentation:** PSPNet with ResNet-101 backbone segments construction objects and traffic signs in camera images. Multi-scale spatial pooling (5×5, 10×10, 25×25) captures elements at varying distances.

**3D Projection:** LiDAR points are projected onto the camera image; each 3D point inherits the segmentation probability at its nearest pixel.

**Non-parametric Memory:** A dynamic graph G = (P, E) where P is the set of all historical and current 3D points, and E connects each point to its K-nearest spatial neighbors. Each point stores R³ coordinates and C-class probability estimates M ∈ [0,1]^C from the image segmenter.

**Occlusion Reasoning:** A dense depth map is computed from the current LiDAR sweep. Past memory points are tested against this depth map to determine whether they are occluded (should be remembered) or have clear line-of-sight but are unobserved (should be forgotten).

**Continuous Convolutions:** Process the memory graph directly in continuous 3D coordinates without voxelization. The learned convolution kernel aggregates current observations with historical beliefs, informed by occlusion features, to produce refined per-point segmentation.

**Memory Update:** After each timestep, new 3D points with their classification probabilities are added to the memory; outdated points are deprecated based on the continuous convolution output.

## Datasets & evaluation

- **Validation:** 62.6% mean IoU (traffic signs 59.1%, construction 66.1%) vs. 59.9% single-sweep CC, 47.3% image baseline, 40.1% voxel NN
- **Test:** 60.2% mean IoU (traffic signs 56.0%, construction 64.4%) vs. 57.3% single-sweep CC, 42.1% image baseline
- Memory-based approach particularly helps for small distant objects that are initially uncertain but become clearer with temporal accumulation
- Occlusion reasoning is critical for avoiding false positive accumulation from objects attached to moving vehicles (construction signs on trucks)

## Limitations

- Evaluated only on construction elements and traffic signs; extension to general object classes not demonstrated
- Requires accurate camera-LiDAR calibration; misalignment causes projection errors at range
- Memory grows dynamically with no explicit size limit; long drives may require memory management strategies
- The 2D segmentation model is a bottleneck — missed detections in the image cannot be recovered by the memory
- Does not model the semantics of construction zone layouts (lane closures, detours) — only individual element detection
- Continuous convolutions are more expensive than voxelized approaches; real-time performance trade-offs not discussed

## Key takeaways

The memory is a dynamically-sized graph of 3D points, each storing classification probabilities from an image segmentation model. At each timestep: (1) a PSPNet-based 2D segmenter produces pixel-wise construction element labels, (2) labels are projected onto LiDAR points via camera-LiDAR calibration, (3) a dense depth map provides occlusion reasoning over past observations, and (4) continuous convolutions over the memory graph aggregate current and historical evidence to produce refined 3D segmentation. The continuous convolution architecture operates directly on arbitrary 3D point coordinates without voxelization, enabling sparse computation at the natural resolution of the sensor data.

Evaluated on 4,000+ snippets from construction zones across North American cities, the method achieves 62.6% mean IoU (validation) vs. 59.9% for single-sweep continuous convolution and 47.3% for the image baseline, demonstrating significant gains from learned spatio-temporal aggregation with occlusion-aware memory.

This work bridges semantic mapping (Kimera, SemanticFusion — using pre-defined Bayesian update rules) with learned spatio-temporal reasoning. Unlike Bayesian fusion methods that use fixed update rules, the continuous convolution learns to weight current vs. past evidence based on occlusion, distance, and noise factors. Unlike recurrent approaches (ConvLSTM) that operate in image space without explicit 3D structure, this memory has a direct mapping to physical 3D coordinates. The non-parametric graph representation anticipates later work on scene graphs for embodied agents, though here focused on a specific safety-critical AV perception task rather than general-purpose memory. From the Uber ATG group (Urtasun), connecting to their broader work on autonomous driving perception.
