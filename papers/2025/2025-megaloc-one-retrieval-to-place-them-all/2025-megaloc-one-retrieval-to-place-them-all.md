---
title: "MegaLoc: One Retrieval to Place Them All"
authors: [Gabriele Berton, Carlo Masone]
year: 2025
venue: ""
tags: [visual-localization]
url: ""
pdf: "[[2025-megaloc-one-retrieval-to-place-them-all.pdf]]"
date_ingested: 2026-06-18
---

# MegaLoc: One Retrieval to Place Them All

![[2025-megaloc-one-retrieval-to-place-them-all-thumbnail.png]]

## Research gap

MegaLoc is a unified image retrieval model achieving state-of-the-art across multiple localization-related tasks: Visual Place Recognition, Landmark Retrieval, Visual Localization, 3D reconstruction, and SLAM. The key insight is that existing methods are task-specific, failing when definitions of "same place" differ (25m distance for VPR vs. landmark identity for LR vs. fine-grained pose proximity for VL). MegaLoc unifies these by training on five diverse datasets (SF-XL, GSV-Cities, MSLS, MegaScenes, ScanNet) with task-specific sampling strategies and multi-similarity loss. Uses DINO-v2-base backbone with SALAD aggregation, frozen except for final transformer layers. Memory-efficient training reduces 360GB to 60GB through dataset-wise backward passes. Achieves SOTA on VPR benchmarks and new SOTA on LaMAR visual localization datasets.

## Contributions

- **Unified retrieval model**: Single model achieving competitive/SOTA performance across Visual Place Recognition, Landmark Retrieval, Visual Localization, 3D Reconstruction, and SLAM
- **Multi-task aware design**: Carefully samples from five datasets with diverse definitions of "same place," bridging task-specific gaps
- **Custom sampling strategies**: Tailored sampling for each dataset (EigenPlaces for SF-XL, CliqueMining for MSLS, visual overlap checks for MegaScenes/ScanNet)
- **Efficient training**: Memory-efficient GPU training (6× reduction) via dataset-wise backward passes enables single A100 GPU training
- **Practical impact**: Eliminates need for separate task-specific models, provides single solution for diverse localization applications

## Method

MegaLoc combines multiple data sources with task-aware sampling:

**Datasets and sampling**:
- **SF-XL**: 41M images from San Francisco; uses EigenPlaces sampling (frontal + lateral perspectives, no visual overlap between places)
- **GSV-Cities**: 530k images, 62k places from 40 cities; direct multi-similarity loss on pre-split non-overlapping classes
- **MSLS**: 1.6M images across 30 cities; CliqueMining to select hard negatives (visually similar but geographically different)
- **MegaScenes**: 2M images from 100k SfM reconstructions; ensures visual overlap between sampled quadruplets (≥1% 3D point overlap)
- **ScanNet**: 2.5M indoor views; ensures spatial proximity (<10m, <30° apart) within quadruplets

**Architecture**:
- DINO-v2-base backbone (frozen except final 4 transformer layers)
- SALAD aggregation layer (64 clusters, 256 channels/cluster)
- Linear projection (16640 → 8448 dimensions)
- L2 normalization
- Multi-similarity loss computed independently per dataset: L = L₁ + L₂ + L₃ + L₄ + L₅ + L₆

**Training**:
- 40k iterations, images resized to 224×224 (inference: 322×322)
- RandAugment data augmentation, AdamW optimizer
- Memory-efficient backward passes per dataset (Algorithm 1)

## Datasets & evaluation

**Visual Place Recognition**:
- State-of-the-art on multiple VPR benchmarks (averaged across evaluation datasets)
- Competitive runtime and parameter efficiency

**Landmark Retrieval**:
- Impressive results on standard LR datasets
- Better generalization to large-scale landmarks than VPR-focused models

**Visual Localization**:
- New state-of-the-art on LaMAR datasets
- Demonstrates impact on end-to-end localization pipelines

**Cross-task robustness**:
- Works well in scenarios requiring retrieval across diverse distance scales (few meters to hundreds of meters)
- Handles mixed small/large scene reconstruction requirements

## Limitations

- How does MegaLoc perform on extremely diverse scenes (e.g., significant temporal changes, urban redevelopment)?
- Can the multi-task training paradigm be extended to even more heterogeneous tasks (e.g., cross-seasonal, cross-dataset)?
- What is the trade-off between unification and specialization—could task-specific fine-tuning on top of MegaLoc improve performance further?
- How does the model handle out-of-distribution domains not covered by the five training datasets?
- Can the approach scale to even larger reference databases or handle real-time streaming scenarios?

## Key takeaways

This work extends visual localization research (e.g., [[2019-hierarchical-localization-at-large-scale|HF-Net]]) by focusing on the retrieval component. While HF-Net addressed hierarchical coarse-to-fine matching, MegaLoc tackles the foundational challenge of retrieving images that work across multiple task definitions. It unifies lessons from VPR (NetVLAD, EigenPlaces), Landmark Retrieval, and 3D Vision communities that had previously diverged in their approaches.
