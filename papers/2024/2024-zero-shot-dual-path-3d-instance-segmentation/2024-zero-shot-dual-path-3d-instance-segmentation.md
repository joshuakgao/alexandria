---
title: "Zero-Shot Dual-Path Integration Framework for Open-Vocabulary 3D Instance Segmentation"
authors: [Tri Ton, Ji Woo Hong, SooHwan Eom, Jun Yeop Shim, Junyeong Kim, Chang Yoo]
year: 2024
venue: "CVPR Workshop"
tags: [3d-scene-understanding, segmentation]
url: ""
pdf: "[[2024-zero-shot-dual-path-3d-instance-segmentation.pdf]]"
date_ingested: 2026-06-18
---

# Zero-Shot Dual-Path Integration Framework for Open-Vocabulary 3D Instance Segmentation

![[2024-zero-shot-dual-path-3d-instance-segmentation-thumbnail.png]]

## Research gap

This paper addresses a key limitation of open-vocabulary 3D instance segmentation: prior methods rely heavily on 3D mask proposals, which work well for common indoor objects but fail on uncommon or unseen object categories. The insight is that 2D open-vocabulary models (like Grounded-SAM) excel at detecting diverse objects that 3D models miss, while 3D models provide more spatially precise masks for common objects. The proposed Zero-Shot Dual-Path Integration Framework complementarily combines both modalities.

## Contributions

- Dual-path framework that equally values 3D point cloud and 2D multi-view image modalities for open-vocabulary 3D instance segmentation
- Conditional Integration with a principled four-scenario adaptive merging strategy based on directional IoU metrics
- Zero-shot, model-agnostic design using only pre-trained components (Mask3D, Grounded-SAM, CLIP)
- Improved performance on ScanNet200, especially on "tail" (uncommon) object categories where 2D models compensate for 3D model blind spots

## Method

1. **3D Pathway**: Mask3D generates class-agnostic 3D instance masks from point clouds. Per-mask CLIP features are extracted by projecting 3D bounding boxes onto the top-k most visible RGB-D images, cropping at multiple levels, and averaging CLIP visual features.

2. **2D Pathway**: Grounded-SAM produces 2D instance masks from RGB-D frames. Masks are projected into 3D using camera intrinsics/extrinsics and depth, then refined via an Instance Fusion Module that accumulates, filters, and merges fragmented 3D projections using IoU and feature similarity.

3. **Dual-Path Integration**:
   - *Dual-modality Proposal Matching*: Compute IoU matrix between all 3D and 2D proposals. Unique proposals (zero overlap with the other modality) go directly to final output.
   - *Adaptive Integration*: For overlapping pairs, compute directional IoUs (IoU_3D = intersection/3D area, IoU_2D = intersection/2D area). Four scenarios:
     - Both high: same object → merge
     - Both low: distinct nearby objects → keep both
     - 2D subset of 3D: 2D captures fine detail → prioritize 2D
     - 3D subset of 2D: 3D captures precise boundary → prioritize 3D

## Datasets & evaluation

- **ScanNet200**: Outperforms single-modality baselines across head/common/tail categories. Strongest gains on "tail" (rare) categories where the 2D pathway detects objects invisible to the 3D model.
- **ARKitScenes**: Qualitative results demonstrate adaptability to diverse indoor environments with open-vocabulary queries.
- Ablation studies show Conditional Integration outperforms naive Simple Integration (combining all proposals), confirming the importance of quality-aware merging.
- The directional IoU thresholds (θ_2D, θ_3D) are determined empirically, with the framework showing reasonable robustness to threshold choices.

## Limitations

- The IoU thresholds (θ_2D, θ_3D) require empirical tuning per dataset, limiting fully automatic deployment
- Processing every 10th frame introduces a trade-off between computation and coverage — important objects in skipped frames may be missed
- The Instance Fusion Module for 2D-to-3D projection can still produce fragmented masks in heavily occluded scenes
- Evaluation is limited to indoor scenes (ScanNet200, ARKitScenes); generalization to outdoor or large-scale environments is untested
- The framework's zero-shot nature means it cannot benefit from task-specific fine-tuning even when labeled data is available

## Key takeaways

The framework has three components. The **3D pathway** uses a pre-trained class-agnostic Mask3D to generate spatially accurate 3D instance masks from point clouds, then extracts CLIP features via multi-level crops of projected bounding boxes. The **2D pathway** uses Grounded-SAM on multi-view RGB-D images to produce 2D mask proposals that are projected into 3D and refined through an Instance Fusion Module. The **Dual-Path Integration** uses a Conditional Integration process with two stages: Dual-modality Proposal Matching (IoU-based identification of unique vs. overlapping proposals across modalities) and Adaptive Integration (four-scenario merging based on directional IoU ratios that determine whether proposals represent the same object, distinct objects, or sub/super-sets of each other).

The framework is entirely zero-shot — it uses only pre-trained models without any fine-tuning on the target dataset. It is model-agnostic, meaning the underlying 2D and 3D models can be swapped for improved components.

Directly addresses limitations of **SAM3D**, which relies solely on 2D-to-3D projection and hierarchical merging. While SAM3D demonstrated that 2D foundation models can be lifted to 3D, it lacks a 3D-native pathway and thus misses spatially precise proposals for common objects. This paper's dual-path approach is complementary.

**OpenMask3D** generates 3D mask proposals and extracts CLIP features but depends entirely on 3D proposal quality. This paper improves on OpenMask3D's CLIP feature extraction by using projected 3D bounding boxes directly (avoiding SAM-mediated point projection errors) and adds the 2D pathway for diverse object detection.

**OVIR-3D** attempts 2D-to-3D instance merging but struggles with large backgrounds. The Instance Fusion Module in this paper addresses similar challenges.
