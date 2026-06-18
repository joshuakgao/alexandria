---
title: "Crowd Source Scene Change Detection and Local Map Update"
authors: [Pair Alignment, D Point Cloud, Deep Learning]
year: 2022
tags: [change-detection, autonomous-vehicles, visual-localization, 3d-scene-understanding]
url: ""
pdf: "[[2022-crowd-source-scene-change-detection.pdf]]"
date_ingested: 2026-06-18
---

# Crowd Source Scene Change Detection and Local Map Update

![[2022-crowd-source-scene-change-detection-thumbnail.png]]

## Research gap

This paper presents a complete pipeline for crowdsource-based scene change detection and local map update, designed as an add-on to existing Visual Positioning Service (VPS) systems. The key insight is that regular VPS localization queries from many users can be repurposed for change detection — no dedicated mapping runs are needed. When enough evidence of change accumulates from crowd queries, the pipeline automatically updates the 3D map by removing obsolete features and augmenting with new SfM-derived points.

## Contributions

- Crowdsource-based change detection using regular VPS queries — no dedicated mapping process required
- Geometric image pair selection (QMGIPS) using VPS poses for reliable query-map comparison
- 5-DOF homography RANSAC for robust urban image alignment (preserves vertical parallels)
- Feature descriptor comparison within visual segments for fine-grained change detection with semantic filtering
- Complete map update pipeline: change aggregation → change propagation → feature deletion → SfM augmentation → 3D-3D registration (RANSAC + ICP)
- Demonstrated that removing outdated features improves both localization speed and accuracy

## Method

**Stage A — VPS:** SLAM SDK produces query image poses, VPS localizes within the existing map. Query images and poses are stored for batch change detection processing.

**Stage B — Change Detection:**
1. *QMGIPS:* Select query-map image pairs where cameras are ≤1m apart with ≤0.2 rad line-of-sight difference
2. *QMALIGN:* 5-DOF homography RANSAC alignment (preserves vertical lines; more robust than 8-DOF for urban scenes); minimum 80 inliers required
3. *Pre-processing:* Semantic segmentation (DeepLab + ResNeSt269, ADE20K) filters sky/ground/people/plants; visual segmentation (DIC) creates fine-grained blobs; depth filtering ignores features <3m from camera
4. *QMCHANGE:* For each blob, compare D2Net descriptors between query and map — classify blob as changed if ratio of unmatched features exceeds threshold; generate per-image change masks
5. *QMPOST:* Aggregate multiple change masks per map image via averaging; propagate changes to other affected map images using NetVLAD retrieval + 3D proximity

**Stage C — Map Update:** SfM on new query images → scale adjustment (Median of Scales) → 3D-3D RANSAC coarse alignment → ICP refinement → delete changed 2D-3D points → add new points to map.

## Datasets & evaluation

- Outperforms SOTA change detection methods on BD and RT benchmarks under viewpoint and illumination variation
- 5-DOF homography produces more inliers and better alignment than 8-DOF for urban scenes
- Change propagation via retrieval extends detection from directly compared images to all affected map images
- Map update (removing obsolete features + augmentation) improves both localization success rate and accuracy
- Evaluated on four datasets including two custom outdoor datasets (D285: 17,652 map images; D457: 13,092 map images)

## Limitations

- Requires sufficient crowd queries from similar viewpoints to accumulate robust change evidence; sparse or uneven coverage areas may not generate enough pairs
- The 5-DOF homography assumption (upright cameras, consistent height) may not hold for all urban scenarios (hills, multi-level structures)
- Semantic filtering relies on ADE20K categories; novel transient objects not in the training set may cause false positives
- Change propagation via retrieval (NetVLAD) can fail in repetitive texture environments
- SfM-based map augmentation is offline and batch-based; real-time incremental update not addressed
- No explicit handling of gradual changes (construction progress, seasonal vegetation) — designed for discrete structural/texture changes

## Key takeaways

The pipeline has three stages: (A) Visual Positioning Service produces query image poses via SLAM + VPS; (B) Crowd Source Change Detection selects geometric query-map image pairs, aligns them via a robust 5-DOF homography RANSAC, and detects changes by comparing D2Net feature descriptors within semantically-filtered visual segments; (C) Local Map Update runs SfM on new query images, registers the new point cloud to the existing map, deletes invalidated 2D-3D points, and adds new ones.

Key technical contributions include: QMGIPS (geometric image pair selection using VPS poses and camera FOV, requiring ≤1m distance and ≤0.2 rad angle), a degenerate 5-DOF homography (preserving vertical lines for urban scenes) that is more robust than standard 8-DOF, semantic filtering to ignore transient objects (sky, people, cars), and visual segmentation-based blob-level change voting. Change masks are aggregated across multiple crowd queries and propagated to all affected map images via visual similarity retrieval.

This paper bridges visual localization (VPS) with map maintenance — using the same infrastructure for both purposes. Unlike Diff-Net (which compares camera vs. rasterized HD map elements), this approach operates on the point cloud + descriptor level, comparing feature descriptors between query images and map images. Unlike POCD (Bayesian object-level tracking), this system works at the image feature level without explicit object detection. The crowdsourcing approach (aggregating evidence from many users) anticipates RTMap's multi-traversal crowdsourced map improvement but at the point cloud / feature level rather than vectorized HD map level. The 5-DOF homography is a practical contribution for urban change detection where buildings provide strong vertical structure. From Toga Networks + Huawei, connecting commercial VPS deployment with map maintenance.
