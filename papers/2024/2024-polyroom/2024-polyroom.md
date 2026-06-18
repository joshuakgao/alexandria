---
title: "PolyRoom: Room-aware Transformer for Floorplan Reconstruction"
authors: [Floorplan Reconstruction Yuzhou Liu, Lingjie Zhu, Xiaodong Ma, Hanqiao Ye, Xiang Gao, Xianwei Zheng, Shuhan Shen]
year: 2024
venue: "ECCV"
tags: [floorplan-reconstruction, 3d-scene-understanding]
url: ""
pdf: "[[2024-polyroom.pdf]]"
date_ingested: 2026-06-18
---

# PolyRoom: Room-aware Transformer for Floorplan Reconstruction

![[2024-polyroom-thumbnail.png]]

## Research gap

PolyRoom frames floorplan reconstruction as a progressive refinement of room polygon queries, building on RoomFormer's two-level query approach while addressing its key limitations: disordered sequences from random query initialization, inaccurate polygons from missing or biased corners, and memory-intensive self-attention. The method introduces three innovations: uniform sampling representation, room-aware query initialization, and room-aware self-attention.

## Contributions

- Uniform sampling floorplan representation with fixed-length vertex sequences enabling dense supervision, angle utilization, and room-aware query initialization
- Room-aware query initialization from instance segmentation masks, replacing random learned queries with geometrically meaningful initial shapes
- Room-aware self-attention decomposition (intra-room + inter-room) reducing memory from O((MN)²) to O(M² + N²) while improving accuracy
- SOTA on Structured3D and SceneCAD with superior generalization across datasets
- Vertex selection combining prediction probabilities with geometric angle priors and polygonization for clean final output

## Method

**Input**: Density map from projected 3D point cloud → CNN backbone (ResNet) → multi-scale features → Transformer encoder with multi-scale deformable self-attention.

**Uniform Sampling Representation**: Each room polygon has exactly N vertices uniformly sampled along its contour. Each vertex has position (x,y) and corner label ∈ {0,1}. Corners from ground truth replace their nearest sampling vertex.

**Room-Aware Query Initialization**: Pre-trained instance segmentation predicts room masks → extract polygon contours → uniformly sample N vertices → initialize room queries Q ∈ R^(M×N×2). Provides geometrically meaningful starting points rather than random embeddings.

**Decoder**: Each layer: room-aware self-attention (intra-room along N vertices + inter-room along M rooms) → multi-scale deformable cross-attention with encoder features → FFN → predict vertex offsets (δx, δy) → update room queries. L=6 layers progressively refine polygons.

**Floorplan Extraction**: Vertices selected by corner probability threshold + angle threshold (exclude flat/acute angles) → polygonization algorithm for minimal, non-redundant vertex sets.

**Loss**: Bipartite matching based on vertex distances. Combined L1 + GIoU loss for vertex positions + cross-entropy for corner labels + cosine similarity for angle supervision.

## Datasets & evaluation

Structured3D: Room F1 comparable to SLIBO-Net, Corner F1 +4.8%, Angle F1 +0.8%. Significantly outperforms RoomFormer across all metrics. SceneCAD: Room IoU +1.1%, Corner F1 +2.0%, Angle F1 +1.6% over RoomFormer. Generalization: superior cross-dataset transfer (trained on Structured3D, tested on SceneCAD) compared to RoomFormer and HEAT. Ablations confirm each component contributes: uniform sampling (+1.4% Corner F1), room-aware query init (+2.1%), room-aware self-attention (+0.8%).

## Limitations

- Relies on pre-trained instance segmentation for query initialization — errors in initial room detection propagate to final polygons
- Uniform sampling with fixed N may struggle with highly complex room shapes requiring more vertices or simple rooms wasting capacity
- Evaluation limited to indoor scenes; outdoor or mixed-use buildings untested
- No semantic room type prediction (unlike RoomFormer which predicts room categories)
- The polygonization post-processing step introduces non-differentiable components that may limit end-to-end optimization

## Key takeaways

Instead of representing room polygons as variable-length corner sequences (RoomFormer), PolyRoom uses a **uniform sampling representation** where each polygon has a fixed number N of uniformly-spaced vertices along its contour, with corner labels indicating which vertices are actual corners. This enables dense supervision along all walls (not just corners), makes angle information usable, and supports room-aware query initialization since the sequence length is fixed.

**Room-aware query initialization** uses a pre-trained instance segmentation network to predict room masks, from which initial polygon contours are extracted and uniformly sampled — providing geometrically meaningful initial queries instead of random learned embeddings. The Transformer decoder then refines these initial shapes layer by layer. **Room-aware self-attention** decomposes vanilla self-attention into intra-room (within a room's vertices) and inter-room (across rooms) components, reducing complexity from O((MN)²) to O(M² + N²) while improving performance.

PolyRoom achieves SOTA on both Structured3D (+4.8% Corner F1, +0.8% Angle F1 over SLIBO-Net) and SceneCAD (+1.1% Room IoU, +2.0% Corner F1, +1.6% Angle F1 over RoomFormer), with better generalization across datasets.

PolyRoom is a direct successor to RoomFormer (already in this topic), sharing the two-level polygon query paradigm but addressing its three main weaknesses. Where RoomFormer uses random learned queries and variable-length corner sequences, PolyRoom uses instance-segmentation-initialized queries and fixed-length uniformly-sampled sequences. The uniform sampling idea trades the elegance of corner-only representation for robustness — dense supervision prevents the catastrophic failures that occur when a single corner is missed or biased.

Compared to HEAT (corner + edge detection), PolyRoom avoids the fundamental problem of missed corners cascading into missing edges and unclosed rooms. Compared to SLIBO-Net (slicing box representation), PolyRoom removes the Manhattan assumption, handling non-axis-aligned walls.

The room-aware self-attention's intra/inter decomposition is reminiscent of Cutie's foreground/background masked attention (segmentation topic) — both decompose attention along semantically meaningful axes to improve both efficiency and accuracy.
