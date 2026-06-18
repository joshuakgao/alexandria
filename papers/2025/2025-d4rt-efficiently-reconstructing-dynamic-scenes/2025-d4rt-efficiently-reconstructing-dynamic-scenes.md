---
title: "D4RT: Efficiently Reconstructing Dynamic Scenes One Query at a Time"
authors: [Chuhan Zhang, Guillaume Le Moing, Skanda Koppula, Ignacio Rocco, Liliane Momeni, Junyu Xie, Shuyang Sun, Sadegh Aliakbarian, Fangyi Zhang, Yuki Asano, Andrea Sherrill, Cees Snoek]
year: 2025
tags: [3d-scene-reconstruction]
url: ""
pdf: "[[2025-d4rt-efficiently-reconstructing-dynamic-scenes.pdf]]"
date_ingested: 2026-06-18
---

# D4RT: Efficiently Reconstructing Dynamic Scenes One Query at a Time

![[2025-d4rt-efficiently-reconstructing-dynamic-scenes-thumbnail.png]]

## Research gap

D4RT is a unified, feedforward transformer architecture for efficient 4D reconstruction (depth + motion + geometry) from single videos. Rather than using task-specific decoders for each modality (depth, optical flow, segmentation, camera), D4RT employs a novel querying mechanism: given a video encoded into a global scene representation, the model can independently probe the 3D position of any spatiotemporal point. This decouples prediction from exhaustive per-frame dense decoding, enabling lightweight, scalable inference. The method jointly estimates depth, spatiotemporal correspondence (point tracks across frames), and full camera parameters (intrinsics + extrinsics) in a single pass. Results show SOTA performance on multiple 4D tasks while maintaining computational efficiency.

## Contributions

- **Unified architecture via querying**: single transformer model handles depth, point tracking, camera estimation through a flexible on-demand point-query interface
- **Efficient decoding**: avoids dense per-frame decoding; queries are computed on-demand, reducing memory and compute
- **4D reconstruction**: simultaneously handles static and dynamic scenes; outputs point clouds, point tracks, camera parameters
- **Scalable design**: lightweight architecture enables fast training and inference; single-stage feedforward (no iterative refinement)
- **SOTA across tasks**: outperforms prior methods (MegaSaM, VGGT, SpatialTrackerV2) on point cloud quality, correspondence accuracy, and camera estimation

## Method

**Architecture:**
- **Encoder**: Self-attention transformer over video frames, producing global scene representation
- **Query interface**: given a 2D source point, source frame, target frame, and camera parameters, the model predicts the 3D position of that point in the target frame
- **Query embedding**: fuses 2D coordinates, frame indices, camera intrinsics via fourier encoding + discrete embeddings
- **Decoder**: outputs 3D position (query-by-query), enabling flexible probing without dense prediction

**Key insight:** shift from "predict everything everywhere" (dense decoding) to "answer arbitrary point queries" (flexible, efficient querying). This enables unified handling of multiple tasks.

## Datasets & evaluation

Competitive or SOTA on:
- **Point cloud reconstruction**: static scenes (buildings, gardens, food items, animals)
- **Point tracking**: dynamic scenes with moving objects; robust 3D trajectories
- **Camera estimation**: full intrinsic + extrinsic parameters from video
- Diverse, in-the-wild videos (landscapes, Colosseum, indoor scenes, sports)

## Limitations

- **Generalization to extreme motion**: unclear how robustly the method handles very fast motion or occlusions
- **Long video sequences**: scalability to very long videos (hours) not explored
- **Outdoor scale**: most results on bounded scenes; outdoor large-scale 4D reconstruction less explored
- **Real-time performance**: efficiency gains over baselines quantified, but absolute latency on edge devices unclear
- **Downstream tasks**: how well do predicted point tracks serve as initialization for downstream task (e.g., video editing, reconstruction refinement)

## Key takeaways

Builds on recent 4D reconstruction work (VGGT, SpatialTrackerV2, MegaSaM) but unifies via a single transformer interface instead of modular off-the-shelf components. Avoids costly iterative refinement of SpatialTrackerV2. Simpler and more scalable than MegaSaM's mosaic of depth/flow/segmentation models.
