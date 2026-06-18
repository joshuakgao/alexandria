---
title: "OpenMask3D: Open-Vocabulary 3D Instance Segmentation"
authors: [Ayça Takmaz, Elisabetta Fedele, Robert Sumner, Marc Pollefeys, Federico Tombari, Francis Engelmann]
year: 2023
venue: "NeurIPS"
tags: [3d-scene-understanding, segmentation, vision-backbone]
url: ""
pdf: "[[2023-openmask3d.pdf]]"
date_ingested: 2026-06-18
---

# OpenMask3D: Open-Vocabulary 3D Instance Segmentation

![[2023-openmask3d-thumbnail.png]]

## Research gap

OpenMask3D introduces the task of open-vocabulary 3D instance segmentation and proposes a zero-shot approach that segments object instances from 3D point clouds based on free-form text queries. The key insight is to compute per-mask CLIP features rather than per-point features — while existing open-vocabulary 3D methods (OpenScene, LERF) produce point-level feature heatmaps that cannot separate instances, OpenMask3D aggregates instance-level features by leveraging class-agnostic 3D mask proposals.

## Contributions

- Introduction of the open-vocabulary 3D instance segmentation task
- Instance-mask-oriented feature computation (per-mask CLIP features) rather than per-point features, enabling instance-level open-vocabulary queries
- Multi-view mask-feature aggregation: visibility-based frame selection → SAM-refined 2D masks → multi-scale CLIP crops → average pooling across views
- Zero-shot approach requiring no training or finetuning beyond the frozen Mask3D and CLIP models
- Demonstrates queries beyond object categories: geometry, affordances, materials, and compositional descriptions ("side table with a flower vase on it")

## Method

**Class-Agnostic Mask Proposals**: Frozen Mask3D (MinkowskiUNet backbone + Transformer decoder) produces binary instance masks. Class predictions are discarded — only the masks are used.

**Frame Selection**: For each 3D mask, compute visibility score s_ij = vis(i,j) / max_j(vis(i,j)) by projecting mask points into each frame, checking FOV and occlusion via depth comparison. Select top-k views with highest visibility.

**2D Mask Refinement**: Project 3D mask points into selected frames. Use RANSAC-style sampling: sample k_sample projected points, run SAM to get 2D mask + confidence score, repeat for k_rounds iterations, keep highest-confidence mask.

**Multi-Scale CLIP Features**: From the refined 2D mask, compute 3 bounding boxes at increasing context levels (tight, +10%, +20%). Pass each crop through CLIP visual encoder. Average-pool across L=3 scales and k views for final per-mask feature.

**Query**: Encode text query with CLIP text encoder. Rank masks by cosine similarity to query feature. Return top masks above threshold.

## Datasets & evaluation

ScanNet200 val: 15.4 AP / 19.9 AP50 / 23.1 AP25 (vs. 11.7/15.2/17.8 for best OpenScene variant). Particularly strong on tail categories (long-tail distribution). Compared to closed-vocabulary Mask3D (26.9 AP), the gap reflects the challenge of open-vocabulary recognition. On Replica, similar trends. Ablations show multi-scale crops (+1.1 AP over single crop), SAM refinement (+0.8 AP over raw projection), and top-k view selection all contribute. Qualitative results demonstrate free-form queries for affordances, geometry, and materials.

## Limitations

- Still significantly behind closed-vocabulary Mask3D (15.4 vs. 26.9 AP), indicating room for improvement in open-vocabulary feature quality
- Relies on frozen Mask3D for mask proposals — mask quality bottlenecks the entire pipeline; missed instances cannot be recovered
- CLIP features may not capture fine-grained spatial relationships within objects
- SAM refinement with RANSAC-style sampling adds computational overhead; deterministic alternatives might be more efficient
- Evaluated only on indoor datasets (ScanNet200, Replica); outdoor and large-scale scenes untested
- The two-stage pipeline prevents end-to-end training or joint optimization of mask proposals and features

## Key takeaways

The two-stage pipeline first obtains class-agnostic 3D instance masks from a frozen Mask3D model (discarding its closed-vocabulary class predictions), then computes a CLIP feature representation for each mask via multi-view fusion. For each mask, the system selects top-k views where the instance is most visible, uses SAM (with RANSAC-style point sampling) to refine 2D projected masks, extracts multi-scale image crops (3 levels of context), passes them through the CLIP visual encoder, and average-pools across crops and views to obtain a single per-mask feature vector. At query time, text queries are encoded with CLIP's text encoder and matched to mask features via cosine similarity.

On ScanNet200, OpenMask3D achieves 15.4 AP (vs. 11.7 for the best OpenScene variant), with particular strength on long-tail categories. It also handles free-form queries describing geometry ("something that is round"), affordances ("something to sit on"), and materials ("wooden furniture").

OpenMask3D builds directly on Mask3D (same author Engelmann) by taking Mask3D's class-agnostic mask proposals and replacing the closed-vocabulary classifier with CLIP-based open-vocabulary features. This demonstrates the modular power of query-based 3D segmentation — the mask proposal module can be decoupled from the recognition module.

Within the 3D scene understanding topic, OpenMask3D bridges Mask3D's 3D-native instance segmentation with the open-vocabulary capability that SAM3D and the Dual-Path Framework also pursue. Where SAM3D lifts 2D SAM masks to 3D and the Dual-Path Framework merges 3D and 2D proposals, OpenMask3D keeps 3D mask proposals fixed and adds open-vocabulary features via multi-view CLIP fusion. HOV-SG (in the embodied-memory topic) uses a similar weighted-sum CLIP feature fusion with DBSCAN filtering for segment-level features.

The per-mask (instance-level) vs. per-point feature distinction is fundamental — OpenScene/LERF produce heatmaps that can't separate instances, while OpenMask3D's mask-level features enable true instance segmentation with open vocabulary.
