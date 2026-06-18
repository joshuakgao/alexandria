---
title: "Connecting the Dots: Floorplan Reconstruction Using Two-Level Queries"
authors: [Yuanwen Yue, Theodora Kontogianni, Konrad Schindler, Francis Engelmann]
year: 2023
venue: "CVPR"
tags: [floorplan-reconstruction, 3d-scene-understanding]
url: ""
pdf: "[[2023-roomformer.pdf]]"
date_ingested: 2026-06-18
---

# Connecting the Dots: Floorplan Reconstruction Using Two-Level Queries

![[2023-roomformer-thumbnail.png]]

## Research gap

RoomFormer reformulates floorplan reconstruction as a single-stage structured prediction problem: given a 3D point cloud projected into a bird's-eye-view density map, directly predict a variable-size set of room polygons, each defined as a variable-length ordered sequence of vertices. This replaces traditional multi-stage pipelines that rely on heuristic intermediate steps (corner detection → edge classification → room assembly or room segmentation → polygon extraction via optimization).

## Contributions

- **Single-stage formulation**: casts floorplan reconstruction as direct set prediction of polygons, eliminating heuristic multi-stage pipelines
- **Two-level polygon queries**: hierarchical query structure with room-level and corner-level queries, enabling variable numbers of rooms with variable numbers of vertices each
- **Polygon matching**: training-time assignment strategy that matches predicted polygons to ground-truth at both room and corner levels for end-to-end supervision
- **Semantic extensions**: straightforward modifications to predict room types, doors, and windows alongside geometry
- **SOTA on Structured3D and SceneCAD**: +1.9 room F1, +4.7 corner F1, +2.9 angle F1 over previous best (HEAT) on Structured3D

## Method

1. **Input**: 3D point cloud projected to bird's-eye-view density map (highlighting walls/structural elements)
2. **Feature backbone**: CNN extracts multi-scale feature maps with positional encodings
3. **Transformer encoder**: multi-scale deformable self-attention refines pixel features with global context
4. **Transformer decoder**: two-level queries (M rooms × N corners × 2D coordinates) are iteratively refined through:
   - Self-attention across all corner queries (enabling cross-room reasoning, e.g., shared walls)
   - Multi-scale deformable cross-attention using polygon queries as reference points for spatial feature pooling
   - FFN predicting valid/invalid class per query to handle variable room/corner counts
5. **Output**: ordered corner sequences per room; connect valid corners in order to obtain room polygons
6. **Polygon matching** (training): Hungarian matching at room level, then cyclic sequence matching at corner level to handle rotational ambiguity of polygon vertex orderings

## Datasets & evaluation

- **Structured3D**: SOTA across all metrics — room F1: +1.9, corner F1: +4.7, angle F1: +2.9 over HEAT (previous best). Significantly faster inference
- **SceneCAD**: SOTA on real-world RGB-D scans with noisier density maps; demonstrates generalization from synthetic to real data
- **Semantic extensions**: room type prediction achieves strong results; door/window prediction viable with simple additional queries
- **Ablations**: two-level query design outperforms flat queries; polygon matching is essential; deformable attention provides efficiency without accuracy loss

## Limitations

- Maximum room and corner counts (M, N) are fixed hyperparameters — very complex layouts with many small rooms or highly irregular polygons may exceed capacity
- Density map projection discards height information — multi-story or split-level buildings not addressed
- Assumes Manhattan-like or near-Manhattan room layouts in practice; very curved or organic geometries may be challenging
- Evaluation limited to Structured3D (synthetic) and SceneCAD (real but small-scale); larger-scale real environments not tested
- No explicit modeling of wall thickness or structural elements beyond doors/windows

## Key takeaways

The model uses a CNN backbone to extract multi-scale features from the density map, followed by a Transformer encoder-decoder with a novel two-level query structure: one level for room polygons (up to M rooms) and one level for corners within each polygon (up to N corners per room). A polygon matching strategy enables end-to-end training by establishing optimal correspondences between predicted and ground-truth polygons at both room and corner levels. The approach achieves state-of-the-art results on Structured3D and SceneCAD with significantly faster inference than prior methods, and can be extended to predict semantic room types, doors, and windows.

Supersedes both top-down methods (Floor-SP, MonteFloor — room segmentation + optimization) and bottom-up methods (FloorNet, HEAT — corner detection + edge classification) by eliminating their heuristic intermediate stages. Draws architectural inspiration from DETR's set prediction paradigm but extends it fundamentally: DETR predicts fixed-size representations (bounding boxes), while RoomFormer generates variable-length ordered sequences (polygons). The two-level query design is novel — prior Transformer-based polygon methods (PolyWorld, BoundaryFormer) assume fixed vertex counts and rely on bounding box initialization.
