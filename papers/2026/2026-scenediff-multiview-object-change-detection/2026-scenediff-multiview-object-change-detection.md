---
title: "SceneDiff: A Benchmark and Method for Multiview Object Change Detection"
authors: [Yuqun Wu, Chih-hao Lin, Henry Che, Aditi Tiwari, Chuhang Zou, Shenlong Wang, Derek Hoiem]
year: 2026
venue: ""
tags: [change-detection, segmentation, construction-monitoring]
url: ""
pdf: "[[2026-scenediff-multiview-object-change-detection.pdf]]"
date_ingested: 2026-06-18
---

# SceneDiff: A Benchmark and Method for Multiview Object Change Detection

![[2026-scenediff-multiview-object-change-detection-thumbnail.png]]

## Research gap

SceneDiff addresses object change detection in real-world scenes captured from different camera viewpoints and at different times. Unlike prior work focusing on satellite imagery or near-duplicate views, this paper tackles the harder problem of identifying what objects have been added, removed, or moved given two video sequences of the same scene filmed with arbitrary camera trajectories. The core insight is that once scenes are geometrically aligned into a shared 3D space using static scene elements, changed objects produce easily detectable geometric and appearance inconsistencies. The approach leverages pretrained foundation models (π³ for 3D alignment, SAM for segmentation, DINO for semantics) and generalizes across domains without retraining.

## Contributions

- **SceneDiff Benchmark**: First instance-level multiview change detection dataset with 350 real-world video pairs spanning 50 diverse scenes and 20 categories; includes dense object instance annotations
- **3D scene alignment via static elements**: Demonstrates that feed-forward 3D models can co-register before/after sequences using static scene geometry, enabling robust change localization despite viewpoint variation
- **Training-free pipeline**: Combines pretrained models (π³, SAM, DINO) for pose estimation → segmentation → change detection; naturally improves as underlying models advance
- **Strong empirical results**: Achieves 53.0% relative AP improvement on multiview benchmarks and 30.6% on two-view benchmarks over prior methods

## Method

SceneDiff operates in three stages:

1. **Image pose estimation**: Uses π³ (Implicit Functional Representations for Image-to-3D) to estimate camera extrinsics and implicitly reconstruct static scene geometry from both before/after video sequences, aligning them into a shared 3D coordinate frame

2. **Object segmentation and tracking**: Applies SAM to segment objects in each frame and matches segments across frames using visual correspondence and optical flow to construct per-object trajectories

3. **Change detection**: For each object, compares semantic features (DINO embeddings) and geometric consistency (3D projection alignment) between before/after states to classify as added, removed, moved, or unchanged

The key insight is decomposing the problem: 3D alignment greatly simplifies change comparison by removing viewpoint confounds. Changed objects show inconsistency in both geometry (3D projection mismatch) and appearance (semantic embedding divergence), making them easy to detect.

## Datasets & evaluation

**Multiview benchmarks:**
- 53.0% relative AP improvement vs. baselines on SceneDiff Benchmark
- Outperforms pixel-level and point-cloud methods that operate without 3D alignment

**Two-view benchmarks (cross-domain evaluation):**
- 30.6% relative improvement on generalized benchmarks
- Zero-shot generalization to unseen scene categories (20 diverse categories in benchmark)

**Applications validated:**
- Construction site monitoring
- Robotic room tidying and manipulation
- Warehouse inventory verification
- Post-disaster damage assessment

## Limitations

- **Occlusion reasoning**: Paper acknowledges occlusions but unclear how robustly the method handles partial visibility across viewpoints
- **Dynamic objects**: All changes assume static background; moving backgrounds (e.g., camera on moving vehicle) not explored
- **Computational cost**: Three-stage pipeline (π³ + SAM + DINO) requires significant inference; deployment latency not reported
- **Static scene assumption**: 3D alignment via static elements; failure modes when scenes have significant dynamic content unclear
- **Generalization across domains**: Benchmark limited to 20 scene categories; applicability to radically different visual domains (e.g., medical imaging, hyperspectral) unexplored

## Key takeaways

The paper introduces SceneDiff Benchmark, the first instance-level multiview change detection dataset comprising 350 diverse scene pairs (200 manually collected, 150 from egocentric video) with dense object-level annotations. They provide a SAM2-based annotation tool and evaluation protocol supporting both per-view and per-scene assessment.

SceneDiff bridges **change detection** (prior work: GeSCF, DynamicEarth on satellite imagery and natural scenes) and **3D scene understanding** (π³ for static reconstruction). Unlike zero-shot foundation model adaptation approaches (GeSCF, DynamicEarth), SceneDiff assumes availability of paired video sequences and leverages 3D alignment as a geometric primitive. Unlike instance segmentation work (SAM), it explicitly handles temporal consistency and occlusion reasoning. The multiview setting and instance-level tracking differentiate it from prior work focusing on semantic or binary change maps.
