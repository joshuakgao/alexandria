---
title: "Point Transformer"
authors: [Hengshuang Zhao, Li Jiang, Jiaya Jia, Philip Torr, Vladlen Koltun]
year: 2021
venue: "ICCV"
tags: [3d-scene-understanding, vision-backbone, segmentation]
url: ""
pdf: "[[2021-point-transformer.pdf]]"
date_ingested: 2026-06-18
---

# Point Transformer

![[2021-point-transformer-thumbnail.png]]

## Research gap

Point Transformer introduces self-attention layers designed specifically for 3D point cloud processing and constructs backbone networks for classification, part segmentation, and semantic scene segmentation. The key insight is that self-attention is inherently a set operator — invariant to permutation and cardinality — making it a natural fit for point clouds, which are sets of points embedded in continuous 3D space.

## Contributions

- Point Transformer layer: vector self-attention with subtraction relation, applied locally within kNN neighborhoods, with position encoding on both attention and value
- Demonstration that vector attention significantly outperforms scalar dot-product attention for point cloud processing (ablation shows +2.3% mIoU)
- Position encoding critical for large-scale scenes (ablation: removing δ drops mIoU by 4.7%)
- U-Net architecture with Point Transformer blocks serving as a general backbone for 3D classification, part segmentation, and semantic segmentation
- SOTA on S3DIS (70.4% mIoU, +3.3 over prior art), ModelNet40 (93.7%), ShapeNetPart (86.6%)

## Method

**Point Transformer Layer**: For each point i with features x_i, compute attention over kNN neighborhood X(i):
y_i = Σ_{x_j ∈ X(i)} ρ(γ(φ(x_i) - ψ(x_j) + δ)) ⊙ (α(x_j) + δ)

Where φ, ψ, α are linear projections, γ is a 2-layer MLP, ρ is softmax normalization, δ is the position encoding (MLP applied to relative positions), and ⊙ is element-wise multiplication (vector attention).

**Position Encoding**: Trainable MLP applied to the 3D coordinates of both query and key points. Added to both the attention computation and the value features — essential for geometric reasoning.

**Network Architecture**: U-Net with 5 encoder stages (N → N/4 → N/16 → N/64 → N/256) using farthest point sampling for downsampling and trilinear interpolation for upsampling. Each stage uses Point Transformer blocks (self-attention + residual MLP). For classification, global average pooling replaces the decoder.

## Datasets & evaluation

S3DIS Area 5: 70.4% mIoU / 76.5% mAcc / 90.8% OA (previous best: 67.1% mIoU). S3DIS 6-fold: 73.5% mIoU (previous: 68.8%). ModelNet40: 93.7% OA / 90.6% mAcc. ShapeNetPart: 86.6% instance mIoU. Ablations: vector attention > scalar attention (+2.3% mIoU); position encoding in both attention and value > attention only (+1.4%) > none (+4.7%); local kNN attention > global attention (both accuracy and efficiency).

## Limitations

- Local kNN attention may miss long-range dependencies that global attention captures; hierarchical design partially addresses this via multi-scale feature propagation
- kNN neighborhood size (k=16) is a fixed hyperparameter; adaptive neighborhoods could better handle varying point densities
- No instance segmentation — purely a backbone; downstream methods (Mask3D) add Transformer decoders for instance prediction
- Computational cost of kNN search at each layer may limit scalability to very large point clouds (millions of points)
- Evaluated on indoor scenes (S3DIS) and objects (ModelNet40, ShapeNetPart); outdoor large-scale performance less established

## Key takeaways

The Point Transformer layer uses **vector self-attention** (not scalar dot-product) applied locally within k-nearest-neighbor neighborhoods. The subtraction relation between query and key features is processed by an MLP to produce per-channel attention weights (vectors, not scalars), enabling different channels to have different aggregation patterns. Crucially, a trainable position encoding δ is added to both the attention vector and the transformed value features, providing geometric awareness that is essential for large-scale scene understanding.

The full network follows a U-Net-style encoder-decoder with 5 stages of transition down (farthest point sampling + MLP) and transition up (interpolation + MLP), with Point Transformer blocks at each resolution level. This design processes large scenes efficiently through hierarchical local attention without quadratic global cost.

Point Transformer sets SOTA on S3DIS Area 5 (70.4% mIoU, first to cross 70%), ModelNet40 classification (93.7% OA), and ShapeNetPart segmentation (86.6% instance mIoU), outperforming prior point-based, voxel-based, and graph-based methods.

Point Transformer is a foundational backbone paper for the 3D scene understanding topic. While Mask3D (2023) and OpenMask3D (2023) use MinkowskiEngine sparse convolution backbones, Point Transformer demonstrates that pure self-attention architectures can match or exceed sparse convolution on point clouds. The vector self-attention design has influenced subsequent work in 3D processing.

Within the topic's architecture progression: PointNet/PointNet++ (pointwise MLP + pooling) → sparse convolution (MinkowskiNet, used by Mask3D) → Point Transformer (local vector self-attention). Each generation adds more expressive local feature interaction while maintaining point cloud invariances.

The local kNN attention design parallels Cutie's masked cross-attention (segmentation topic) — both restrict attention to semantically or spatially relevant subsets for efficiency and focus.
