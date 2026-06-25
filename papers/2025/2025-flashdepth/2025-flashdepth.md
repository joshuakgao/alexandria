---
title: "FlashDepth: Real-time Streaming Video Depth Estimation at 2K Resolution"
authors: [Gene Chou, Wenqi Xian, Guandao Yang, Mohamed Abdelfattah, Bharath Hariharan, Noah Snavely, Ning Yu, Paul Debevec]
year: 2025
venue: "CVPR 2025"
tags: [depth-estimation]
url: "https://arxiv.org/abs/2504.07093"
pdf: "[[2025-flashdepth.pdf]]"
date_ingested: 2026-06-25
---

# FlashDepth: Real-time Streaming Video Depth Estimation at 2K Resolution

![[2025-flashdepth-thumbnail.png]]

## Research gap
Existing video depth estimation methods cannot simultaneously achieve accuracy/consistency, high resolution, and real-time streaming. Diffusion-based methods (DepthCrafter) are accurate but slow (2.1 FPS at 1024x576) and require batch processing. Optimization-based methods need the full video upfront. Prior online/streaming methods use lightweight architectures with poor accuracy. No method could process 2K streaming video at real-time rates with competitive accuracy.

## Contributions
- A real-time streaming video depth model that processes 2044x1148 frames at 24 FPS on a single A100 GPU, satisfying all three requirements (accuracy, high resolution, streaming) simultaneously.
- A lightweight Mamba-based recurrent module (~1% of total parameters) that aligns intermediate DPT features to a consistent scale across frames on-the-fly, avoiding the need to align final depth maps.
- A hybrid dual-resolution architecture where a small ViT (ViT-S) processes full 2K resolution for sharp boundaries while a larger ViT (ViT-L) processes downsampled frames for accurate features, fused via cross-attention.
- Demonstration that building on pre-trained single-image models (Depth Anything v2) with minimal additional data circumvents the shortage of high-quality video depth training data.

## Method
FlashDepth builds on Depth Anything v2 (DAv2), which uses a DINOv2 ViT encoder with a DPT decoder. Two key additions enable streaming video depth at 2K resolution:

**Temporal consistency via Mamba:** A recurrent Mamba module processes downsampled intermediate features from the DPT decoder to align scale across frames. The module maintains a hidden state encoding past frame context. It is placed after the DPT decoder but before the final convolution head, and its output layer is zero-initialized so that the first iteration produces a valid single-image depth map. The module adds ~1% parameters and negligible latency due to downsampled input.

**Hybrid dual-resolution model:** Two streams run in parallel — FlashDepth-S (ViT-S at full 2K resolution) preserves sharp boundaries, while FlashDepth-L (ViT-L at 924x518) provides accurate features. Intermediate features from the large model are fused into the small model via cross-attention, also zero-initialized. The key insight is that numerical depth accuracy is resolution-independent (boundaries account for <1% of pixels), but boundary sharpness requires high-resolution processing.

Training uses a combination of synthetic datasets (Hypersim, TartanAir, Spring) and real video data (Waymo), with scale-and-shift-invariant loss.

## Datasets & evaluation
Evaluated on multiple unseen datasets including ETH3D, KITTI, Sintel, Bonn, ScanNet, and in-the-wild videos. FlashDepth achieves competitive or superior accuracy compared to state-of-the-art batch methods (DepthCrafter, Video Depth Anything) while running at 24 FPS at 2K — 11x faster than DepthCrafter (2.1 FPS at 1024x576). Boundary sharpness significantly exceeds all baselines, particularly for thin structures (tree leaves, fur, wires). The model generalizes to long sequences, moving objects, and large camera pose changes without per-video optimization.

## Limitations
- Requires an A100 GPU for real-time 2K performance; consumer hardware throughput is not reported.
- Produces affine-invariant (relative) depth, not metric depth — requires external scale reference for metric applications.
- The Mamba hidden state may drift on extremely long sequences, though the paper shows robustness on tested lengths.
- Training data is limited to driving and synthetic indoor scenes; performance on highly out-of-distribution domains (underwater, aerial, medical) is untested.
- The hybrid architecture ties the method to the specific ViT-S/ViT-L + DPT design; it is unclear how the approach transfers to other backbone families.

## Key takeaways
- The bottleneck for real-time video depth is not accuracy but temporal consistency and resolution scaling. Separating these concerns — using a lightweight recurrent module for consistency and a dual-resolution hybrid for sharp boundaries — solves both without sacrificing speed.
- Aligning intermediate features rather than final depth maps is computationally cheaper and empirically more effective for temporal consistency.
- Resolution matters for boundary sharpness but not for numerical accuracy (<1% of pixels are boundaries), motivating the asymmetric dual-resolution design.
- Building on a strong pre-trained single-image model (DAv2) and adding minimal video-specific components is more practical than training video diffusion models from scratch, especially given the shortage of high-quality video depth data.
