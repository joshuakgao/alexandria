---
title: "GeoTransformer: Fast and Robust Point Cloud Registration with Geometric Transformer"
authors: [Zheng Qin, Hao Yu, Changjian Wang, Yulan Guo, Yuxing Peng, Slobodan Ilic, Dewen Hu, Zhi Xu]
year: 2023
venue: "TPAMI 2023"
tags: [point-cloud-registration]
url: ""
pdf: "[[2023-geotransformer.pdf]]"
date_ingested: 2026-06-18
---

# GeoTransformer: Fast and Robust Point Cloud Registration with Geometric Transformer

![[2023-geotransformer-thumbnail.png]]

## Research gap

GeoTransformer addresses point cloud registration in challenging low-overlap scenarios by learning robust geometric features without requiring RANSAC. The method follows a coarse-to-fine approach: first matching downsampled superpoints based on patch overlap, then propagating correspondences to dense points. The key innovation is encoding pairwise distances and triplet-wise angles to maintain invariance to rigid transformations while capturing spatial geometry. By removing the need for RANSAC in alignment estimation, GeoTransformer achieves 100× acceleration compared to traditional methods while significantly improving inlier ratios across diverse benchmarks (indoor, outdoor, synthetic, multiway, and non-rigid).

## Contributions

- **Geometric Transformer architecture**: Encodes pairwise distances and triplet-wise angles for rotation-invariant feature learning
- **RANSAC-free registration**: High matching accuracy eliminates need for RANSAC, achieving 100× speedup in alignment
- **Spatial consistency preservation**: Geometric constraints maintain coherent patch matchings even in low-overlap and symmetric regions
- **Coarse-to-fine propagation**: Efficient superpoint matching followed by dense point correspondence propagation
- **Comprehensive evaluation**: Demonstrates effectiveness across multiple modalities and overlap scenarios, with 18–31 percentage point inlier ratio improvement on 3DLoMatch

## Method

GeoTransformer processes point clouds through several stages:

1. **Superpoint sampling**: Both point clouds are downsampled into superpoints (geometric patches)
2. **Feature encoding**: Geometric transformer encodes pairwise distances (equivariant to rotation) and triplet-wise angles (invariant to rotation) to capture local geometric structure
3. **Superpoint matching**: Cross-attention mechanism matches superpoints based on whether their local neighborhoods overlap
4. **Dense propagation**: Patch-level correspondences are propagated to individual points
5. **Transformation estimation**: Unlike traditional methods, the high-quality correspondences allow direct least-squares fitting without RANSAC

The geometric consistency constraints help resolve ambiguities in symmetric or low-overlap regions where vanilla transformers would fail.

## Datasets & evaluation

- **3DLoMatch (low-overlap benchmark)**: Inlier ratio 94.5% (from 22.7%), recall 77% (best reported)
- **3DMatch (high-overlap)**: Maintains competitive performance while excelling at low-overlap
- **Generalization**: Performs well across indoor, outdoor, synthetic, and non-rigid scenarios without retraining
- **Efficiency**: 100× faster than RANSAC-based methods due to elimination of robust estimation step

## Limitations

- Scalability to very large point clouds or extreme low-overlap scenarios (< 5%)
- Extension to non-rigid deformation where geometric constraints may conflict with shape changes
- Analysis of failure cases and how geometric invariance sometimes over-constrains matching in certain symmetries

## Key takeaways

Builds on recent keypoint-free methods that downsample to superpoints rather than detecting repeatable keypoints. Improves upon vanilla transformers for point matching by incorporating explicit geometric constraints. Contrasts with traditional correspondence-based methods that rely on keypoint detection (difficult in low-overlap cases) and RANSAC estimation (computationally expensive).
