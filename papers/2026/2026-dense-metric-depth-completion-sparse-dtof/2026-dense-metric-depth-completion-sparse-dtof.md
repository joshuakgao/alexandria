---
title: "Dense Metric Depth Completion from Sparse Direct Time-of-Flight Sensors"
authors: [Hakyeong Kim, Ruicheng Wang, Chengtang Yao, Jiaolong Yang, Min H. Kim]
year: 2026
venue: "CVPR 2026"
tags: [depth-estimation]
url: "https://openaccess.thecvf.com/content/CVPR2026/papers/Kim_Dense_Metric_Depth_Completion_from_Sparse_Direct_Time-of-Flight_Sensors_CVPR_2026_paper.pdf"
date_ingested: 2026-08-09
---

# Dense Metric Depth Completion from Sparse Direct Time-of-Flight Sensors

![[2026-dense-metric-depth-completion-sparse-dtof-thumbnail.png]]

## Research gap

Monocular depth estimators produce impressive relative depth but suffer from scale ambiguity and cannot recover absolute metric depth reliably. Direct Time-of-Flight (dToF) sensors provide accurate metric depth but produce extremely sparse, low-resolution, noisy outputs. Existing depth completion methods are typically designed for a specific sensor and fail under extreme sparsity, irregular sampling patterns, or noise from different dToF device families. Recent approaches using diffusion models or iterative optimization achieve strong accuracy but are too slow for real-time or mobile deployment.

## Contributions

- A generalizable framework for dense metric depth completion from sparse dToF measurements that works across diverse sensor types, sparsity levels, and noise conditions with a single model trained entirely on synthetic data.
- A depth-guided dual-branch ViT encoder with masked joint attention that enables depth tokens to guide RGB features while preventing RGB signals from corrupting the sparse depth representation.
- A comprehensive dToF simulation pipeline that models flash, sub-VGA flash, and rotating (LiDAR) sensor families, including realistic noise, sparsity patterns, and hardware artifacts, enabling strong synthetic-to-real transfer.

## Method

The model takes an RGB image and sparse dToF depth as input. Preprocessing normalizes the sparse depth via log-normalization and flood-fill upsampling, converting it into a 3-channel tensor (two depth channels + validity mask) compatible with a pretrained DINOv2 ViT encoder.

The core architecture is a **depth-guided dual-branch encoder** with two parallel ViT branches (one for RGB, one for depth), both initialized from DINOv2-Small. The branches interact through **masked joint attention**: image and depth tokens are concatenated, and joint queries/keys/values are computed, but a directional mask suppresses image-to-depth attention while allowing depth-to-image attention. This asymmetry lets depth measurements guide image features without being overwritten by potentially unreliable RGB cues.

A lightweight DPT-style decoder with reduced feature dimensions (384→256→64→32→16) predicts normalized dense depth and a validity mask. Metric depth is recovered via de-normalization using the log-scale parameters from preprocessing, allowing extrapolation beyond the original sensor range.

The loss combines four terms: depth-weighted L1 (inverse-depth weighting for near-range accuracy), global scale-invariant loss, local patch-wise scale-invariant loss (both in 3D point cloud space using ROE solver), and binary cross-entropy mask loss.

The **dToF simulation pipeline** models three sensor families:
- **Flash dToF** (mobile): 64–10K random sparse points
- **Sub-VGA flash dToF**: downsampled dense depth with edge erosion/dilation via Perlin noise
- **Rotating dToF** (LiDAR): line-structured scans simulating Velodyne VLP-16/32 specifications

Noise augmentations include Gaussian depth-proportional noise, spatial jitter, outlier injection (0.2–1%), and depth inpainting augmentation (random square/edge/Perlin-noise mask removal) to simulate occlusions and missing returns.

## Datasets & evaluation

Trained entirely on synthetic RGB-D data. Evaluated zero-shot on 6 benchmarks across 3 real dToF devices:

- **Real sensor**: KITTI-DC (Velodyne LiDAR, Rel 2.00 vs. OMNI-DC's 1.48), ZJUL5 (VL53L5CX 8×8 sensor, Rel 11.13, tied for best)
- **Simulated sparse**: DDAD (Luminar-H2, Rel 4.61, best), DIODE (Rel 0.89), ETH3D (Rel 0.84, best), iBims-1 (Rel 1.29)

Achieves the best average performance (Rel 3.46, δ 77.9) across all benchmarks while running at **34ms inference, 0.44GB memory** — 19× faster than OMNI-DC and 190× faster than Marigold-DC, with comparable or lower memory.

## Limitations

- Zero-shot performance on KITTI-DC (Rel 2.00) trails OMNI-DC (1.48), which uses iterative optimization at inference time — suggesting that for specific known sensors, specialized or optimization-based methods may still have an edge.
- The ViT-Small backbone limits capacity; scaling to ViT-Base/Large could improve accuracy but at the cost of the model's efficiency advantage.
- Trained only on synthetic data — while this enables generalization, there may be real-world artifacts or sensor behaviors not captured by the simulation pipeline.
- The validity mask prediction relies on binary cross-entropy, which may not handle gradual confidence degradation (e.g., semi-transparent surfaces) well.

## Key takeaways

- Masked joint attention is a principled mechanism for asymmetric cross-modal fusion: depth informs vision but not vice versa, preventing the more reliable modality from being corrupted.
- A well-designed sensor simulation pipeline can replace real paired training data entirely — the model never sees real dToF data during training yet generalizes across 3 real sensor families.
- The efficiency gap is dramatic: achieving near-SOTA accuracy at 34ms with 0.44GB memory makes this practical for mobile and real-time deployment, while diffusion/optimization approaches require seconds and gigabytes.
- Log-normalization of depth with DINOv2 initialization is an effective trick for repurposing pretrained vision encoders for depth processing without architectural changes.
- The asymmetric attention design (depth guides RGB, not vice versa) reflects a broader principle: when fusing modalities of different reliability, information flow should be directional.
