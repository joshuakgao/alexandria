---
title: "HOV-SG: Hierarchical Open-Vocabulary 3D Scene Graphs for Language-Grounded Robot Navigation"
authors: [Abdelrhman Werby, Chenguang Huang, Martin Büchner, Abhinav Valada, Wolfram Burgard]
year: 2024
tags: [embodied-memory, scene-graphs, 3d-scene-understanding]
url: ""
pdf: "[[2024-hov-sg.pdf]]"
date_ingested: 2026-06-18
---

# HOV-SG: Hierarchical Open-Vocabulary 3D Scene Graphs for Language-Grounded Robot Navigation

![[2024-hov-sg-thumbnail.png]]

## Research gap

HOV-SG constructs hierarchical open-vocabulary 3D scene graphs for language-grounded robot navigation in large-scale multi-floor indoor environments. The system builds a four-level hierarchy — building → floors → rooms → objects — where each node is enriched with open-vocabulary CLIP features. This enables natural language queries at multiple abstraction levels (e.g., "find the blue chair in the bedroom on the third floor") rather than just object-level goals.

## Contributions

- Hierarchical open-vocabulary scene graph with floor, room, and object levels, each enriched with CLIP features for multi-level language grounding
- Weighted-sum CLIP feature fusion (global + crop + masked crop) with DBSCAN-based majority vote filtering for robust segment-level features
- View-embedding room classification using k-means clustering of room camera CLIP features, outperforming unprivileged LLM-based baselines
- Cross-floor Voronoi navigational graph enabling actionable multi-story navigation from abstract language queries
- Novel AUC_top-k metric for measuring true open-vocabulary semantic accuracy across variable-size label sets
- 75% reduction in representation size compared to dense open-vocabulary maps

## Method

**3D Segment-Level Mapping**: SAM extracts class-agnostic 2D masks per frame, which are backprojected to 3D. Segments are incrementally merged via overlap graphs (fully connected, weight = overlap ratio, subgraph merging). For each 2D mask, three CLIP features are computed — global frame, bounding box crop, and masked crop without background — and fused via weighted sum. Point-wise features are averaged across frames, then per-segment features are obtained via DBSCAN clustering of all point features within a segment, selecting the feature closest to the majority cluster mean.

**Floor Segmentation**: Height histogram (0.01m bins) over all points → peak detection → DBSCAN clustering of peaks → consecutive height pairs define floor boundaries.

**Room Segmentation**: Per-floor BEV histogram → wall skeleton via thresholding → Euclidean distance field → region seeds → Watershed algorithm. Room nodes are enriched with k=10 representative CLIP view embeddings via k-means clustering of associated camera views.

**Object Association**: Objects assigned to rooms by BEV point cloud overlap. Object nodes carry their segment CLIP feature and 3D point cloud.

**Navigation**: Hierarchical query decomposition (floor → room → object) via LLM, cosine similarity scoring at each level, path planning on Voronoi graph.

## Datasets & evaluation

Open-vocabulary 3D semantic segmentation (ViT-H-14): Replica mIoU 0.231 vs. 0.18 ConceptGraphs; ScanNet mIoU 0.222 vs. 0.16 ConceptGraphs. Floor segmentation: 100% accuracy across all scenes. Room classification: 84.10% Acc≈ (matching GPT-4 with GT objects at 84.25%), far exceeding unprivileged GPT-4 (62.55%). Representation size: 75% smaller than dense open-vocabulary maps. Real-world multi-floor navigation demonstrated on mobile robot.

## Limitations

- Reconstruction preprocessing is expensive (hours per scene), limiting real-time applicability — Memory Over Maps achieves 100x faster indexing for object-level tasks
- Room segmentation relies on geometric heuristics (Watershed) that struggle with open floor plans and semantically ambiguous layouts
- CLIP features may lack fine-grained spatial reasoning for distinguishing similar objects in different contexts
- The fixed floor-room-object hierarchy may not capture all spatial relationships (e.g., objects spanning rooms, outdoor areas)
- Navigation relies on pre-recorded RGB-D walks with accurate odometry; robustness to SLAM drift is not evaluated
- Object-level semantic accuracy, while best among zero-shot methods, still significantly lags behind fully supervised approaches (MinkowskiNet)

## Key takeaways

The pipeline has two main stages. First, a 3D segment-level open-vocabulary map is built by extracting SAM masks per frame, backprojecting to 3D, incrementally merging segments via overlap graphs, and computing segment-level CLIP features through a weighted-sum fusion of global, crop, and masked-crop image encodings with DBSCAN-based majority vote filtering. Second, the hierarchical scene graph is constructed: floors are segmented via height histogram peak detection, rooms via BEV Watershed segmentation with view-embedding classification (k-means over room camera CLIP features), and objects are associated with rooms by spatial overlap. A cross-floor Voronoi navigational graph enables actionable path planning.

HOV-SG achieves state-of-the-art open-vocabulary 3D semantic segmentation on ScanNet and Replica, outperforming ConceptGraphs and ConceptFusion by large margins. Room classification via view embeddings (84.1% Acc≈) matches privileged GPT-4 baselines that use ground-truth object categories. The representation is 75% more compact than dense open-vocabulary maps. Real-world multi-floor robot navigation is demonstrated.

HOV-SG bridges Hydra's hierarchical scene graph structure with open-vocabulary features. Where Hydra uses closed-set semantics, HOV-SG equips every level of the hierarchy with CLIP features. Compared to ConceptGraphs (flat object graph, small scenes), HOV-SG adds floor and room levels and scales to multi-story buildings.

Within the embodied memory landscape, HOV-SG represents the **reconstruction-first** paradigm that Memory Over Maps directly challenges. HOV-SG invests heavily in offline 3D reconstruction and scene graph construction to enable hierarchical language-grounded navigation. Memory Over Maps shows this reconstruction can be bypassed for object-level localization, but HOV-SG's room and floor hierarchy enables query types (spatial containment, multi-level grounding) that map-free approaches cannot yet support.

The DBSCAN-based feature filtering anticipates the information-theoretic retrieval ideas in STaR — both address the problem of noisy/redundant features, though at different levels (per-segment features vs. retrieved evidence sets).
