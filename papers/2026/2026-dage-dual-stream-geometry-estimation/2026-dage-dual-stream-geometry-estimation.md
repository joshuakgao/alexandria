---
title: "DAGE: Dual-Stream Architecture for Efficient and Fine-Grained Geometry Estimation"
authors: [Tuan Duc Ngo, Jiahui Huang, Seoung Wug Oh, Kevin Blackburn-Matzen, Evangelos Kalogerakis, Chuang Gan, Leng Jia]
year: 2026
tags: [3d-scene-reconstruction, depth-estimation]
url: ""
pdf: "[[2026-dage-dual-stream-geometry-estimation.pdf]]"
date_ingested: 2026-06-18
---

# DAGE: Dual-Stream Architecture for Efficient and Fine-Grained Geometry Estimation

![[2026-dage-dual-stream-geometry-estimation-thumbnail.png]]

## Research gap

This paper presents DAGE, a dual-stream transformer architecture that decouples global geometric coherence from fine-grained detail preservation in video 3D reconstruction. The core challenge is that state-of-the-art feed-forward methods (e.g., VGGT, Pi3) rely on global attention to enforce multi-view consistency, but the quadratic scaling of attention limits both spatial resolution and sequence length. DAGE solves this by running global attention on aggressively downsampled frames (≤252px) in a low-resolution stream, while a separate high-resolution stream processes original images independently to preserve sharp boundaries and fine structures. A lightweight cross-attention adapter fuses these streams before the final geometry heads. This design enables processing up to 2K resolution and 1000+ frames while maintaining 2-28x speedup over prior SOTA. DAGE achieves state-of-the-art results on video geometry and depth-sharpness benchmarks.

## Contributions

- **Dual-stream architecture**: Decouples global consistency (low-resolution stream with alternating frame/global attention) from detail preservation (high-resolution per-frame stream)
- **Lightweight adapter**: Cross-attention fusion of LR and HR tokens with novel positional encoding strategies (interpolated RoPE for resolution generalization, grid-snapping for cross-scale alignment)
- **Resolution and sequence length independence**: Supports flexible input resolutions (up to 2K) and long sequences (1000+ frames) with tractable runtime, enabling substantially faster inference than prior work
- **Knowledge distillation**: Supervises LR stream with features from pre-trained teacher (Pi3) to maintain pose accuracy despite aggressive downsampling

## Method

**Architecture**:

1. **Low-Resolution Stream**:
   - Processes downsampled frames (long side ≤252px)
   - DINOv2 tokenizer + alternating frame-wise and global self-attention blocks
   - Outputs global feature tokens F_lr for all frames
   - Supervised via feature distillation from pre-trained Pi3 teacher
   - Predicts camera poses and metric scale (no per-frame high-frequency detail)

2. **High-Resolution Stream**:
   - Processes each frame independently at native resolution
   - Uses frozen 24-layer ViT encoder from MoGe2 [100]
   - Outputs per-frame feature tokens F_hr
   - Preserves sharp details and zero-shot generalization from single-image models

3. **Adapter** (lightweight fusion):
   - Five [CrossAttn → SelfAttn] blocks
   - Cross-attention: HR queries attend to LR keys/values
   - Position encoding strategies:
     - Self-attention: interpolated RoPE for extrapolation-safe high-resolution processing
     - Cross-attention: grid-snapping to align HR tokens to nearest LR grid cells, avoiding extrapolation
   - Output: fused features F_fuse

4. **Prediction Heads**:
   - Dense geometry: Feature pyramid upsampling to regress pointmaps at original resolution
   - Camera pose: MLP on pooled LR features
   - Metric scale: Single scene-wide scale factor from metric scale token

**Training**:
- Pointmap loss (ℓ1 with ROE alignment)
- Camera loss (geodesic distance for rotation, ℓ1 for translation on relative poses)
- Metric scale loss (for datasets with metric supervision)
- Normal loss (on synthetic data)
- Gradient loss (on synthetic data, multi-scale inverse-depth gradients)
- Feature distillation loss (supervise LR stream with Pi3 teacher)

## Datasets & evaluation

**Evaluation benchmarks**: Video geometry estimation on 8 datasets (GMU Kitchens, ScanNet, KITTI, Sintel, Monkaa, UrbanSyn, Unreal4K, Diode) ranging from ~640p to 2K resolution

**Key metrics**:
- Relative point error (Rel_p) ↓
- Inlier ratio (δ_p) ↑ at 0.25 threshold
- Comparison: single-image methods, video diffusion models, set-based visual-geometry models (VGGT, Pi3)

**Performance**:
- State-of-the-art on video geometry and depth-sharpness benchmarks
- Competitive on 3D reconstruction and camera pose estimation
- **Speed**: 2× faster at 540p, 28× faster at 2K compared to Pi3
- **Memory**: Lower GPU memory footprint than prior methods
- **Scalability**: Handles 1000+ frames with metric-scale 3D geometry (demonstrated on indoor scene: 10.8m × 17.2m)

## Limitations

- **Adapter design complexity**: Cross-scale fusion via grid-snapping is effective but somewhat heuristic; other attention-based fusion strategies explored in ablations (Sec. 4.6)
- **Knowledge distillation dependency**: LR stream initialization from Pi3 teacher crucial for pose accuracy; unclear how architecture generalizes without pre-trained teacher
- **Detail preservation trade-offs**: Per-frame HR stream preserves detail but may miss subtle inter-frame consistency; shown to maintain good cross-view consistency via adapter (Sec. 4.6)
- **Dynamic scenes**: Evaluated primarily on static/mostly-static scenes; behavior on highly dynamic content (scene flow, occlusions) less explored
- **Real-world deployment**: Requires unfrozen HR encoder (MoGe2) and distillation setup; not end-to-end learnable from scratch

## Key takeaways

**Positioning**: DAGE advances video-based 3D reconstruction by addressing the resolution-consistency trade-off:

- **Single-view methods** (DepthAnything V2, DepthPro, MoGe): Sharp detail but no temporal/multi-view consistency; adapted methods for video are compute-intensive
- **Video geometry (VGGT, Pi3)**: Strong multi-view consistency but blurred details and limited resolution/sequence length due to global attention scaling
- **DAGE**: Combines benefits of both—sharp detail preservation from single-image models + global consistency from video methods, without resolution/sequence constraints

Extends π³ (permutation-equivariant reconstruction) and D4RT (4D reconstruction) by operating at higher resolution with explicit fine-detail preservation. Complements diffusion-based approaches (compute-heavy but good priors) with feed-forward efficiency.
