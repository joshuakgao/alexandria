---
title: "Sparse 3D Topological Graphs for Micro-Aerial Vehicle Planning"
authors: [Helen Oleynikova, Zachary Taylor, Roland Siegwart, Juan Nieto Autonomous Systems Lab]
year: 2018
venue: "IROS 2018"
tags: [embodied-ai]
url: ""
pdf: "[[2018-sparse-3d-topological-graphs.pdf]]"
date_ingested: 2026-06-18
---

# Sparse 3D Topological Graphs for Micro-Aerial Vehicle Planning

![[2018-sparse-3d-topological-graphs-thumbnail.png]]

## Research gap

This paper presents a method for extracting sparse topological graphs from noisy 3D voxel maps for MAV path planning. Starting from a Euclidean Signed Distance Field (ESDF) built from RGB-D sensor data (via voxblox), the method computes a 3D Generalized Voronoi Diagram (GVD) — the set of points equidistant from multiple obstacles — then thins it into a one-voxel-thick skeleton diagram representing the topology of the free space. This skeleton is converted into a sparse graph of vertices connected by straight-line edges, suitable for rapid global planning.

## Contributions

- Novel method for extracting thin 3D skeleton diagrams from noisy real-world ESDF data, extending 2D GVD techniques to 3D
- Sparse graph construction from the skeleton that is resistant to noise and voxel resolution changes
- Orders-of-magnitude faster global planning compared to A* and RRT methods
- Validated on real maps built onboard an MAV using RGB-D sensing
- Open-source implementation as part of voxblox

## Method

**ESDF Construction**: Dense voxel grid storing signed distance to nearest obstacle, built incrementally from RGB-D data using voxblox.

**GVD/Medial Axis Extraction**: For each free-space voxel, compare parent direction (to closest obstacle) with 6-connected neighbors. A voxel is on the medial axis if its basis points are sufficiently distinct (θ-SMA criterion: angle between parent directions exceeds threshold θ). This filters spurious edges from sensor noise.

**Edge Extraction & Thinning**: Classify medial axis voxels as edges (≥18 of 26-connected neighbors also on medial axis). Apply topology-preserving erosion (deletion templates from [Palágyi 1998]) to thin to one-voxel-thick skeleton while maintaining connectivity.

**Sparse Graph Fitting**: Walk along skeleton edges, placing graph vertices at endpoints and high-curvature points. Connect vertices with straight-line edges. Reconnect disconnected subgraphs by finding shortest paths through the ESDF between components.

**Planning**: A* search on the sparse graph for initial path → polynomial trajectory optimization for dynamically-feasible path → collision re-optimization against the original map.

## Datasets & evaluation

Planning speedup: 1-3 orders of magnitude faster than A* on the full voxel grid and RRT*. Graph sparsity: hundreds of vertices representing environments of millions of voxels. Noise resistance: graphs from noisy sensor data maintain the same topological structure as noise-free versions. Resolution independence: similar graph structure across different voxel sizes. Real-world validation: maps built onboard MAV in machine hall and office environments.

## Limitations

- No semantic information — purely geometric topology; subsequent work (Hydra) adds semantic layers
- ESDF construction assumes static environment; dynamic obstacles would require incremental GVD updates
- Skeleton thinning heuristics (θ threshold, neighbor count) require tuning per environment type
- Sparse graph may miss narrow passages if the GVD edge is pruned during noise filtering
- Planning only considers obstacle clearance; task-relevant objectives (coverage, information gain) not incorporated
- Limited to indoor/structured environments; outdoor terrain with uneven ground would require different treatment

## Key takeaways

The key challenge is handling noise: real-world sensor data produces many spurious Voronoi edges (as shown in Fig. 3). The paper addresses this with θ-SMA (simplified medial axis) filtering based on minimum angles between basis points, topology-preserving erosion for thinning, and subgraph reconnection to maintain connectivity. The resulting sparse graph is resistant to noise and resolution changes.

Planning on the sparse graph achieves orders-of-magnitude speedup over grid-based A* and sampling-based RRT methods while maintaining path quality. Paths through the graph maximize obstacle clearance (by construction of the Voronoi diagram) and can be refined with polynomial trajectory optimization for dynamically-feasible MAV flight.

This paper is foundational infrastructure for the embodied memory topic. Hydra (2022) directly builds on this work — using the same ESDF → GVD → skeleton → topological graph pipeline (from the same lab, ETH Zürich/MIT SPARK) as the "places" layer of its hierarchical scene graph. HOV-SG and MoMa-LLM similarly use Voronoi graphs for navigation, citing this lineage.

The key connection to embodied memory is that the topological graph serves as a compact, navigable spatial memory of the environment — a sparse representation of where the robot can go, extracted from dense geometric data. While this paper focuses purely on planning (no semantics), subsequent work (Hydra, HOV-SG, MoMa-LLM) enriches these graphs with semantic attributes, room classification, and object associations, turning a planning data structure into a memory representation.

Compared to 2D topological planning (Thrun 1998, Lau et al.), this paper extends the approach to full 3D — critical for MAVs that need to plan paths at different heights and through complex 3D structures.
