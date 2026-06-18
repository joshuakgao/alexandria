---
title: "π³: Permutation-Equivariant Visual Geometry Learning"
authors: [Yifan Wang, Jianjun Zhou, Haoyi Zhu, Wenzheng Chang, Yang Zhou, Zizun Li, Junyi Chen, Jiangmiao Pang, Chunhua Shen, Tong He]
year: 2026
venue: "ICLR"
tags: [3d-scene-reconstruction, depth-estimation]
url: ""
pdf: "[[2026-pi3-permutation-equivariant-visual-geometry-learning.pdf]]"
date_ingested: 2026-06-18
---

# π³: Permutation-Equivariant Visual Geometry Learning

![[2026-pi3-permutation-equivariant-visual-geometry-learning-thumbnail.png]]

## Research gap

π³ addresses a fundamental limitation in visual geometry reconstruction: the reliance on selecting a fixed reference view to establish a global coordinate system. This inductive bias, inherited from classical Structure-from-Motion and Multi-view Stereo approaches, creates unnecessary constraints—a poor choice of reference view can catastrophically degrade reconstruction quality. π³ eliminates this bottleneck by introducing a fully permutation-equivariant architecture that predicts affine-invariant camera poses and scale-invariant local point maps without designating any reference frame. Instead of anchoring to a specific viewpoint, the model predicts camera extrinsics and point geometry in each frame's own coordinate system, ensuring that the same inputs always produce the same outputs regardless of input ordering. The approach works seamlessly on single images, video sequences, or unordered image sets from both static and dynamic scenes, achieving state-of-the-art performance across diverse benchmarks (indoor, outdoor, aerial, cartoons).

## Contributions

- **Permutation-equivariant architecture**: Fully equivariant design (no positional embeddings tied to frame indices) enables consistent, order-independent predictions; each frame yields the same affine-invariant camera pose and scale-invariant point map regardless of input sequence order
- **Reference-view-agnostic reconstruction**: Eliminates the need to select a fixed reference view; predicts geometry in each frame's own camera coordinate system; solves a core problem in classical and modern visual geometry methods
- **Robustness to input ordering**: Empirically demonstrates dramatic performance stability across different input orderings (ACC drops from 0.95 in VGGT to 0.12 in π³ when reference frame changes)
- **Multi-task unification**: Single model handles camera pose estimation, monocular depth, video depth, and dense 3D point reconstruction across diverse domains (open-domain images, cartoons, dynamic content)
- **State-of-the-art results**: Outperforms VGGT and other SOTA methods on ETH3D, ScanNet, KITTI, Sintel, Real10K, and other benchmarks

## Method

π³ employs a transformer-based architecture with the following key design choices:

1. **Permutation equivariance**: Avoids frame-index-dependent positional embeddings; instead uses content-based representations that are invariant to input ordering
2. **Alternating view-wise and global self-attention**: View-wise attention processes individual frames; global attention enables cross-view communication and geometry alignment
3. **Affine-invariant camera pose prediction**: Predicts camera extrinsics in a way that is invariant to global affine transformations; eliminates scale and translation ambiguities across frames
4. **Scale-invariant local point maps**: Each frame's 3D points are predicted in its own camera frame, avoiding the need for a shared global coordinate system
5. **Feed-forward inference**: Single forward pass produces complete geometry (poses and dense point maps) without iterative refinement

The architecture is trained end-to-end on diverse visual geometry datasets, optimizing for pose accuracy and dense depth/point prediction.

## Datasets & evaluation

**Camera pose estimation:**
- Strong performance on standard benchmarks (ETH3D, ScanNet, KITTI)
- Robust to input ordering (Figure 2: >8× more stable than VGGT across reference frame changes)

**Depth estimation:**
- Monocular depth: State-of-the-art on single-image tasks
- Video depth: Competitive or superior on video sequences (Sintel, dynamic scenes)

**3D point reconstruction:**
- Dense point map prediction on diverse domain imagery (Real10K, cartoons, aerial)
- Handles both static scenes and dynamic content (moving objects, people)

**Benchmark coverage:**
- Evaluated on 10+ benchmarks spanning indoor (ScanNet, ETH3D), outdoor (KITTI), synthetic (Sintel), and open-domain scenarios
- Consistent improvements over VGGT and Fast3R baselines

## Limitations

- **Computational cost**: Transformer architecture with view-wise and global attention may be expensive on large image collections; inference time comparison with baselines not detailed
- **Absolute scale ambiguity**: While scale-invariant representation is elegant, applications requiring metric absolute scale (robotics, autonomous vehicles) may need post-hoc scale recovery mechanisms
- **Dynamic scenes**: Paper claims to handle dynamic content, but method for separating dynamic vs. static geometry, or tracking moving objects, not fully explained
- **Generalization to extreme viewpoint changes**: Robustness on highly sparse/wide-baseline image sets or extreme lighting variations not thoroughly explored
- **Comparison with classical methods**: Quantitative comparison with classical SfM/MVS pipelines (e.g., COLMAP) would ground the work in the traditional computer vision literature

## Key takeaways

π³ builds on recent feed-forward geometry reconstruction methods (DUSt3R, VGGT, Fast3R) but fundamentally addresses their shared limitation: reference-view dependency. Unlike these methods that treat one frame as a privileged reference, π³ adopts a symmetric, permutation-equivariant design. This connects to work on equivariant neural networks (Kondor & Trivedi, Weiler et al.) but applies the principle to 3D geometry in a novel way. The work also relates to classical Structure-from-Motion literature (Hartley & Zisserman, Furukawa et al.) which similarly struggles with reference-frame selection.
