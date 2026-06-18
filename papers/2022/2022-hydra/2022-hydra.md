---
title: "Hydra: A Real-time Spatial Perception System for 3D Scene Graph Construction and Optimization"
authors: [Nathan Hughes, Yun Chang, Luca Carlone]
year: 2022
venue: "Robotics: Science and Systems (RSS)"
tags: [scene-graphs, slam]
url: ""
pdf: "[[2022-hydra.pdf]]"
date_ingested: 2026-06-18
---

# Hydra: A Real-time Spatial Perception System for 3D Scene Graph Construction and Optimization

![[2022-hydra-thumbnail.png]]

## Research gap

Hydra is a real-time system for building hierarchical 3D scene graphs from sensor data as a robot explores an environment. The system constructs a five-layer graph representation: metric-semantic 3D mesh at the lowest level, objects and agents, places (topological map), rooms, and buildings. A key contribution is making this hierarchical construction work in real-time despite the computational complexity—previous offline methods could take minutes. The system combines fast early-stage perception (feature tracking, depth reconstruction), mid-level perception (incremental mesh and place extraction), and slower high-level perception (loop closure detection and optimization). It achieves accuracy comparable to batch offline methods while running online.

## Contributions

- **Real-time incremental algorithms** for constructing multi-layer scene graphs using local Euclidean Signed Distance Functions (ESDF) and Generalized Voronoi Diagrams
- **Hierarchical loop closure detection** that compares statistics across scene graph layers (places, objects, appearance) rather than relying solely on visual features
- **Unified scene graph optimization** using embedded deformation graphs to simultaneously correct all layers (mesh, places, objects, rooms) after a loop closure
- **Parallelized architecture** that balances computation across three perception tiers to achieve real-time performance on multi-core CPUs

## Method

**Scene Graph Construction (Layers 1-3):**
- Uses a local ESDF computed around the robot's current location (8m radius) to bound memory usage
- Extracts places from the ESDF using the Generalized Voronoi Diagram (GVD), avoiding expensive batch processing
- Incrementally sparsifies the GVD into a topological graph of places by selecting key nodes and connecting them
- Segments objects via Euclidean clustering of semantic-labeled mesh vertices

**Room Segmentation (Layer 4):**
- Novel approach based on dilation of the place graph: as obstacles dilate by increasing distances δ, places that are too close to walls disappear, naturally partitioning the graph into disconnected components (rooms)
- Uses greedy modularity-based community detection to assign remaining places to rooms

**Loop Closure Detection & Optimization:**
- Top-down detection: constructs hierarchical descriptors at each agent node (place-based, object-based, appearance-based) and compares them across past poses
- Bottom-up verification: if a match is found, attempts geometric registration using RANSAC (for features) or TEASER++ (for objects)
- Optimization: builds a deformation graph from agent poses, mesh control points, and the place graph, then solves an optimization problem to minimize deformations while satisfying loop closure constraints

**Architecture:**
- Low-level (sensor rate): feature tracking, depth reconstruction
- Mid-level (5-10 Hz): VIO backend, mesh/place extraction, object detection, scene graph frontend
- High-level (variable rate): loop closure detection, scene graph backend optimization, room detection

## Datasets & evaluation

- **Accuracy**: On simulated (uHumans2) and real (SidPac building) datasets, achieves object detection accuracy (80-100% found and correct) and place position errors (20cm) comparable to ground-truth batch methods
- **Room detection**: Outperforms prior work (Kimera) on multi-floor environments; achieves consistent precision/recall vs. Kimera's oscillation between extremes
- **Loop closures**: Hierarchical scene graph-based detection finds ~2x more high-quality loop closures within 10cm error compared to vision-only baselines
- **Runtime**: Mid-level perception runs in constant time regardless of environment size; high-level optimization cost grows gradually (not linearly) with map size; comparable to batch methods despite online operation
- **Embedded deployment**: Runs on Nvidia Xavier NX at ~5 Hz for objects/places/rooms, demonstrating practical applicability

## Limitations

- Room segmentation fails in open-plan environments (semantically distinct rooms with no topological separation)
- Scene graph nodes remain partially unlabeled (e.g., rooms are not labeled as "kitchen" or "bedroom")
- Scene graph optimization approach could benefit from recent advances in pose graph sparsification
- Limited exploration of how scene graphs support downstream tasks (planning, prediction, decision-making)
- Computational overhead on embedded platforms still has margin for optimization

## Key takeaways

Builds directly on prior work by the same group (Kimera, 3D Dynamic Scene Graphs) by making the hierarchical construction real-time. Key innovations: windowing the ESDF to control memory, incremental GVD-based place extraction (vs. batch processing the entire ESDF), community detection for room segmentation (vs. height-based heuristics), and unified scene graph optimization (vs. separate optimizations of trajectory and map). Outperforms concurrent object-centric scene graph work (which is real-time but lacks hierarchical structure) and offline approaches (which are hierarchical but too slow).
