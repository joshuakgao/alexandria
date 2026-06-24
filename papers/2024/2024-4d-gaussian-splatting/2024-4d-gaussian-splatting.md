---
title: "4D Gaussian Splatting for Real-Time Dynamic Scene Rendering"
authors: [Guanjun Wu, Taoran Yi, Jiemin Fang, Lingxi Xie, Xiaopeng Zhang, Wei Wei, Wenyu Liu, Qi Tian, Xinggang Wang]
year: 2024
venue: "CVPR 2024"
tags: [gaussian-splatting]
url: ""
pdf: "[[2024-4d-gaussian-splatting.pdf]]"
date_ingested: 2026-06-18
---

# 4D Gaussian Splatting for Real-Time Dynamic Scene Rendering

![[2024-4d-gaussian-splatting-thumbnail.png]]

## Research gap

This paper extends 3D Gaussian Splatting to dynamic scenes by introducing 4D Gaussian Splatting (4D-GS), a holistic representation that maintains a single set of canonical 3D Gaussians and deforms them across time using an efficient Gaussian deformation field network. Rather than storing separate 3D Gaussians per timestep (which scales as O(tN)), 4D-GS achieves O(N+F) memory complexity where F is the deformation network size.

## Contributions

- A unified 4D Gaussian splatting framework that models both Gaussian motion and shape deformation across time with a single canonical Gaussian set
- A spatial-temporal structure encoder using multi-resolution HexPlane decomposition that connects nearby 3D Gaussians for more accurate motion prediction
- A multi-head Gaussian deformation decoder with separate heads for position, rotation, and scaling deformations
- Real-time rendering (82 FPS at 800×800) with state-of-the-art quality, fast training (8 min), and compact storage (18 MB)

## Method

The framework maintains canonical 3D Gaussians G and a Gaussian deformation field network F. At each timestamp t, the deformation field predicts offsets ΔG = F(G, t) to produce deformed Gaussians G' = G + ΔG, which are then rendered via standard differentiable splatting.

**Spatial-Temporal Structure Encoder:** Uses 6 multi-resolution 2D planes (HexPlane decomposition) — three spatial (xy, xz, yz) and three temporal (xt, yt, zt). For each Gaussian, features are queried via bilinear interpolation from each plane and aggregated through outer product, then passed through a tiny MLP.

**Multi-head Gaussian Deformation Decoder:** Three separate MLPs predict deformations for position (Δx,Δy,Δz), rotation (Δr), and scaling (Δs). The deformed Gaussians are rendered using standard 3D-GS differential splatting.

**Optimization:** 3D Gaussians are initialized with 3,000 iterations of static training (warm-up), then jointly optimized with the deformation field using L1 color loss and grid-based total-variation regularization.

## Datasets & evaluation

**Synthetic (D-NeRF dataset):** 34.05 dB PSNR (best), 0.98 SSIM, 82 FPS, 18 MB storage, 8 min training — surpassing all baselines in quality while being 50x faster than TiNeuVox and orders of magnitude faster than NeRF-based methods.

**Real-world (HyperNeRF, Neu3D):** Competitive quality with prior methods while achieving real-time rendering (34 FPS on HyperNeRF, 30 FPS on Neu3D). Storage remains compact at 18-57 MB depending on scene complexity.

Ablation studies confirm the importance of the multi-resolution HexPlane encoder, the warm-up initialization, and the multi-head decoder design.

## Limitations

- Performance degrades with extremely fast or complex motions that cannot be captured by smooth deformation fields
- Topological changes (objects appearing/disappearing) are not explicitly modeled
- The method assumes a single canonical space, which may struggle with scenes containing multiple independently moving objects
- Real-world performance (30 FPS) is lower than synthetic (82 FPS) due to scene complexity and higher resolution
- Integration with downstream tasks like dynamic scene editing and 4D tracking is demonstrated but not fully developed

## Key takeaways

The deformation field consists of two components: a spatial-temporal structure encoder using multi-resolution HexPlane decomposition to efficiently encode 4D neural voxels into 6 plane features, and a lightweight multi-head decoder that predicts per-Gaussian deformations in position, rotation, and scaling. The HexPlane decomposition decomposes a 4D voxel grid into six 2D planes (xy, xz, yz, xt, yt, zt), enabling nearby Gaussians to share spatial-temporal information through bilinear interpolation.

4D-GS achieves real-time rendering at 82 FPS (800×800) on synthetic datasets and 30 FPS (1352×1014) on real-world datasets using a single RTX 3090 GPU, while matching or exceeding the rendering quality of prior state-of-the-art methods like D-NeRF, TiNeuVox, and KPlanes. Training takes only 8 minutes on synthetic scenes.

4D-GS builds on 3D Gaussian Splatting by adding temporal modeling, taking a fundamentally different approach from concurrent works: Dynamic3DGS stores per-timestep Gaussians (O(tN) memory), 4D Gaussian (Yang et al.) adds marginal temporal distributions but limits temporal scope, and Deformable-3DGS uses a plain MLP without spatial-temporal structure encoding. The HexPlane decomposition draws from KPlanes and HexPlane for NeRFs, adapting the factorized 4D representation to the Gaussian splatting framework. Compared to dynamic NeRFs (D-NeRF, TiNeuVox), 4D-GS achieves comparable quality at 30-80x the rendering speed by replacing volume rendering with splatting.
