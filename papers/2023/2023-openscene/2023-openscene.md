---
title: "OpenScene: 3D Scene Understanding with Open Vocabularies"
authors: [Songyou Peng, Kyle Genova, Chiyu Jiang, Andrea Tagliasacchi, Marc Pollefeys, Thomas Funkhouser]
year: 2023
venue: "CVPR 2023"
tags: [3d-scene-understanding, segmentation, vision-backbone]
url: ""
pdf: "[[2023-openscene.pdf]]"
date_ingested: 2026-06-18
---

# OpenScene: 3D Scene Understanding with Open Vocabularies

![[2023-openscene-thumbnail.png]]

## Research gap

OpenScene proposes a zero-shot approach for open-vocabulary 3D scene understanding by computing dense per-point features that are co-embedded with text and image pixels in the CLIP feature space. Given a 3D point cloud with posed RGB images, the system produces a feature vector for every 3D point that can be queried with arbitrary text strings — enabling not just object recognition but queries about materials ("metal"), affordances ("sit"), activities ("work"), room types ("kitchen"), and physical properties ("soft"), all without any labeled 3D data.

## Contributions

- Dense per-point CLIP feature computation for 3D scenes via multi-view fusion + 3D distillation, enabling open-vocabulary queries on any text concept
- 2D-3D ensemble that combines image-derived semantic features with geometry-derived structural features for robust per-point representations
- Zero-shot 3D semantic segmentation that surpasses fully-supervised methods on large label sets (40+ classes) and rare/tail categories
- Demonstration of diverse query types beyond object categories: materials, affordances, activities, room types, physical properties
- Task-agnostic features — same model supports semantic segmentation, object search, scene exploration, and affordance estimation

## Method

**2D Multi-View Fusion**: For each 3D point, project into all posed images. Extract per-pixel features using a pre-trained 2D open-vocabulary model (OpenSeg or LSeg). Aggregate features across views via weighted averaging (based on viewing angle and distance).

**3D Feature Distillation**: Train a MinkowskiNet (sparse 3D convolution) to predict CLIP features from 3D point coordinates + colors alone. Supervised by the 2D-fused features as pseudo ground truth. This learns geometric patterns (e.g., flat horizontal surfaces → tables) that complement 2D appearance.

**2D-3D Ensemble**: Final per-point feature is a weighted combination of the 2D-fused and 3D-distilled features. The ensemble consistently outperforms either alone across all datasets and metrics.

**Querying**: Encode any text query with CLIP text encoder. Compute cosine similarity with all per-point features → heatmap over the 3D scene. For semantic segmentation, compute similarity to all candidate class labels and assign argmax.

## Datasets & evaluation

Zero-shot 3D semantic segmentation: 62.8% mIoU on 4 unseen ScanNet classes (vs. 7.7% for 3DGenz, the prior SOTA zero-shot method). On full ScanNet 20-class: below fully-supervised SOTA but comparable to supervised methods from a few years prior. Critical finding: on Matterport3D with K=160 classes, OpenScene surpasses the fully-supervised approach because supervised methods collapse on tail classes. The 2D-3D ensemble improves over either pathway alone on all datasets. Also evaluated on outdoor nuScenes — demonstrates cross-domain applicability.

## Limitations

- Per-point features cannot separate object instances — the fundamental limitation that motivates OpenMask3D
- Performance below fully-supervised methods on standard benchmarks with small label sets (20 classes)
- Quality depends heavily on the 2D open-vocabulary model (OpenSeg vs. LSeg) — improvements in 2D features directly transfer to 3D
- Multi-view fusion is computationally expensive for large scenes with many images
- The 3D distillation network learns to hallucinate features for unseen geometry but may produce unreliable features for novel 3D structures
- Heatmap output requires manual thresholding for binary segmentation decisions

## Key takeaways

The approach has two complementary pathways: (1) **2D multi-view fusion** — for each 3D point, back-project into all posed images, extract per-pixel CLIP features (from OpenSeg or LSeg), and aggregate across views; (2) **3D distillation** — train a sparse 3D convolutional network (MinkowskiNet) to predict CLIP features from 3D geometry alone, using the 2D-fused features as pseudo-supervision. The final per-point feature is a 2D-3D ensemble of both pathways, combining image-derived semantic richness with geometry-derived structural understanding.

On zero-shot 3D semantic segmentation, OpenScene dramatically outperforms prior work (62.8% vs. 7.7% mIoU on 4 unseen ScanNet classes). More importantly, it surpasses fully-supervised methods when the number of classes grows large (40+), because supervised approaches fail on tail classes while OpenScene's CLIP features generalize to arbitrary categories.

OpenScene is the foundational per-point open-vocabulary method that OpenMask3D explicitly builds upon and addresses the limitations of. OpenMask3D's key critique is that OpenScene's per-point features produce heatmaps that cannot separate individual object instances — multiple chairs produce a single "chair" heat blob. OpenMask3D solves this by computing per-mask features instead of per-point features.

Within the 3D scene understanding topic, OpenScene occupies the "semantic field" paradigm alongside LERF (NeRF-based), while Mask3D/OpenMask3D/Dual-Path represent the "instance mask" paradigm. HOV-SG (embodied-ai topic) uses a similar multi-view CLIP feature fusion strategy at the segment level rather than the point level, with DBSCAN-based filtering to address the noise that per-point approaches suffer from.

OpenScene's strength — task-agnostic features supporting diverse query types — remains unmatched by instance-based methods, which focus primarily on object-category queries.
