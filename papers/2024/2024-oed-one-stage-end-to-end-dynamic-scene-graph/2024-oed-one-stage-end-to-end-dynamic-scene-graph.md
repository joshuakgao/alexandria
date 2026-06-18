---
title: "OED: Towards One-stage End-to-End Dynamic Scene Graph Generation"
authors: [Guan Wang, Zhimin Li, Qingchao Chen, Yang Liu]
year: 2024
tags: [dynamic-scene-graph-generation, scene-graphs, video-understanding]
url: ""
pdf: "[[2024-oed-one-stage-end-to-end-dynamic-scene-graph.pdf]]"
date_ingested: 2026-06-18
---

# OED: Towards One-stage End-to-End Dynamic Scene Graph Generation

![[2024-oed-one-stage-end-to-end-dynamic-scene-graph-thumbnail.png]]

## Research gap

OED proposes a one-stage end-to-end framework for dynamic scene graph generation that eliminates the multi-stage pipeline (detection → tracking → relation classification) used in prior work. Rather than treating DSGG as sequential subtasks optimized independently, OED reformulates it as a set prediction problem using pairwise features to directly predict subject-object-relation triples. The framework introduces a Progressively Refined Module (PRM) for aggregating temporal context without requiring separate trackers or handcrafted trajectories, enabling joint end-to-end optimization. Experiments on Action Genome demonstrate competitive performance while reducing computational overhead.

## Contributions

- One-stage end-to-end framework eliminating explicit detection and tracking stages
- Reformulation of DSGG as set prediction problem with pairwise feature representation
- Progressively Refined Module (PRM) for temporal context aggregation without external trackers
- Demonstrates that end-to-end optimization avoids sub-optimal solutions from independent stage optimization
- Reduces computational redundancy from enumerating negative subject-object pairs

## Method

**One-Stage Framework**: Directly generates dynamic scene graphs from video sequences without independent object detection or tracking. Uses a CNN backbone and transformer encoder to extract visual features, then processes spatial and temporal information jointly.

**Pairwise Feature Representation**: Encodes each subject-object pair as a feature vector, enabling direct prediction of relationships without exhaustively enumerating all candidate pairs. Reduces computational cost and negative sample bias.

**Progressively Refined Module (PRM)**: Aggregates temporal context across frames without requiring external tracking. Refines predictions progressively by considering reference frames and their relationships to the target frame, allowing the model to capture temporal dependencies implicitly.

**Spatial-Temporal Context Aggregation**: Spatial context aggregation uses pair-wise instance and relation decoders within frames. Temporal aggregation progressively refines predictions by incorporating information from reference frames through self-attention and cross-attention mechanisms.

## Datasets & evaluation

- Achieves competitive or state-of-the-art results on Action Genome benchmark for DSGG
- Demonstrates reduced computational overhead compared to multi-stage methods
- Ablation studies validate the contribution of the Progressively Refined Module
- Shows that end-to-end training improves performance over pipelined approaches
- More efficient than methods requiring explicit detection and tracking

## Limitations

- How well does PRM generalize to videos with significant appearance changes or occlusions?
- Scalability to longer videos with very high object counts
- Applicability beyond the Action Genome dataset to diverse real-world scenarios
- Trade-offs between end-to-end optimization and interpretability of intermediate results
- Extension to scene graphs with richer attribute information beyond binary relationships

## Key takeaways

Advances beyond STTran (2021) by eliminating the separate spatial encoder and temporal decoder stages. While STTran required sequential encoding then decoding, OED's one-stage approach integrates spatial-temporal reasoning. Directly addresses limitations of multi-stage methods that suffer from error propagation and suboptimal joint solutions. References transformer-based DETR-like approaches applied to tracking and extends them to the full DSGG task.
