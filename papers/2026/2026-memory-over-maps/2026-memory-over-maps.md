---
title: "Memory Over Maps: 3D Object Localization Without Reconstruction"
authors: [Xander Yap, Jianwen Cao, Allison Lau, Boyang Sun, Marc Pollefeys]
year: 2026
tags: [embodied-memory]
url: ""
pdf: "[[2026-memory-over-maps.pdf]]"
date_ingested: 2026-06-18
---

# Memory Over Maps: 3D Object Localization Without Reconstruction

![[2026-memory-over-maps-thumbnail.png]]

## Research gap

Memory Over Maps proposes a map-free pipeline for open-vocabulary 3D object localization that entirely eliminates the need for explicit 3D scene reconstruction. Instead of building point clouds, voxel grids, or scene graphs from pre-scanned RGB-D video, the system stores only a compact set of posed RGB-D keyframes as a lightweight visual memory bank. Keyframes are selected via pose-based thresholds (15° rotation, 0.25m translation), yielding a compact database that can be further compressed by downsampling image resolution.

## Contributions

- A map-free, open-vocabulary target localization framework that operates directly on posed RGB-D keyframes, removing the need for any global 3D reconstruction
- Two-stage hybrid retrieval combining fast SigLIP2+FAISS embedding search with selective VLM re-ranking, reducing VLM queries from N to K while maintaining reasoning depth
- Robust 3D localization via text-prompted segmentation (SAM 3), depth backprojection, HDBSCAN outlier removal, and multi-view spatial fusion
- Over 100x faster scene indexing compared to reconstruction-based methods, with substantially lower storage requirements
- State-of-the-art or competitive performance on GOAT-Core and multiple object-goal navigation benchmarks without any task-specific training

## Method

The pipeline operates in three stages on a pre-scanned RGB-D video with known camera poses:

**Scene Representation**: Keyframes are extracted via pose-based selection (rotation/translation thresholds). Each keyframe stores an RGB image, depth map, and camera-to-world pose. SigLIP2 image embeddings are pre-computed and indexed with FAISS.

**Two-Stage Hybrid Retrieval**: Stage 1 uses cosine similarity over SigLIP2 embeddings for sub-millisecond top-K retrieval with deduplication (cosine > 0.9 threshold). Stage 2 sends K candidates to a VLM with a constrained prompt ("Is a {query} visible? Reply yes/no ⟨visibility 0–10⟩"), discarding negatives and re-ranking by confidence.

**3D Object Localization**: SAM 3 segments the target in each retrieved view using the query as text prompt. Masked pixels within valid depth range are backprojected to world coordinates via the pinhole camera model. Predictions are grouped into object instances by cloud proximity (overlap ≥15% or min distance <0.5m). Multi-view fusion expands each cluster by checking nearby cameras, and HDBSCAN removes outliers. The bounding box center of the fused cloud serves as the localization prediction.

**Navigation**: A pre-trained DD-PPO PointNav policy navigates to the goal with dynamic goal tracking (projecting the object cloud into the current view), multi-goal fallback, and visibility-based stopping.

## Datasets & evaluation

On GOAT-Core localization (SR@5), the method achieves 90.1% on the 4ok scene (vs. 86.7% for LagMemo, the previous best) and 77.2% on the harder 5cd scene. It is particularly strong on image queries (85.4% vs. 73.0% for LagMemo on 4ok) and language queries (92.7% on 4ok). On HM3D ObjectNav, it achieves 71.0% SR and 38.8% SPL, competitive with trained methods. Scene indexing takes ~1 minute vs. hours for reconstruction-based methods.

## Limitations

- Performance on the Nfv (HM3D) scene (71.6%) falls below HOV-SG (82.4%), suggesting reconstruction-based methods may retain advantages in certain environments
- The approach assumes pre-scanned RGB-D video with known poses — the keyframe memory must be built before deployment, limiting applicability to truly unknown environments
- Multi-view fusion relies on accurate depth and camera poses; degraded depth sensors or SLAM drift could undermine 3D estimates
- The VLM re-ranking stage, while reduced to K queries, still introduces latency and cost that scales with the number of queries
- Whether this map-free approach extends to manipulation tasks (which Chameleon argues require fine-grained perceptual memory) is untested

## Key takeaways

At query time, a three-stage pipeline converts a multimodal goal (language, category, or image) into a 3D target estimate: (1) SigLIP2 embeddings indexed with FAISS retrieve top-K candidate views in sub-second time; (2) a VLM re-ranks candidates with structured prompts, filtering false positives while scoring fine-grained attributes; (3) SAM 3 segments the target in retrieved views, masked depth is backprojected to 3D, predictions are grouped into object instances via spatial proximity, and per-instance multi-view fusion produces a robust 3D goal estimate. The system then navigates to the goal using a pre-trained PointNav policy with dynamic goal tracking and multi-goal fallback.

The approach is entirely training-free and achieves strong performance across GOAT-Core localization (90.1% SR@5 on the 4ok scene) and object-goal navigation benchmarks, while requiring over two orders of magnitude less preprocessing time and substantially less storage than reconstruction-based pipelines like HOV-SG and OpenFunGraph. Real-world deployment on a ground robot validates the full pipeline.

This paper takes the most extreme position in the "images as memory" trend identified by SnapMem — not just using raw images for reasoning, but eliminating 3D reconstruction entirely from the localization pipeline. Where SnapMem still operates within an exploration paradigm, Memory Over Maps assumes a pre-scanned environment and optimizes for efficiency.

The two-stage retrieval (embedding + VLM re-ranking) shares the scalable retrieval philosophy of STaR and EmbodiedLGR, but applies it to localization rather than QA. The on-demand 3D estimation via depth backprojection is a minimal counterpart to R⁴'s SLAM-anchored object database — constructing geometry only when needed rather than maintaining persistent 3D state.

Compared to reconstruction-based methods (HOV-SG, OpenMask3D, VLMaps), the approach trades geometric completeness for orders-of-magnitude faster preprocessing and competitive or superior localization accuracy, suggesting that dense 3D maps may be over-engineered for the specific task of object-goal navigation.
