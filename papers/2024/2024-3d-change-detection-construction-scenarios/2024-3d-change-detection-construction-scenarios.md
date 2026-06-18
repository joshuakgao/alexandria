---
title: "Three-dimensional Change Detection and Description in Complex Construction Scenarios"
authors: [Xuming Ge, Xinjing Liu]
year: 2024
venue: "ISPRS"
tags: [construction-monitoring, change-detection, point-cloud-registration]
url: ""
pdf: "[[2024-3d-change-detection-construction-scenarios.pdf]]"
date_ingested: 2026-06-18
---

# Three-dimensional Change Detection and Description in Complex Construction Scenarios

![[2024-3d-change-detection-construction-scenarios-thumbnail.png]]

## Research gap

This paper addresses change detection and semantic description in large-scale construction scenarios using laser point clouds. Construction sites present unique challenges: numerous objects with frequent spatial and postural changes, complex and varied transformations, noise and imperfections in point cloud data, and difficulty in distinguishing effective changes from sensor artifacts. The authors propose a novel workflow that combines improved octree-based change detection (OCD) with semantic characterization and quantization of changes. Rather than detecting raw changes, they introduce six semantic change types (appeared, disappeared, partially disappeared, growth, moved, rotated) that describe the change process itself. The method is validated on multi-temporal point cloud data from bridge construction projects, successfully identifying changing entities and computing construction progress metrics. Key contribution: focuses on semantic description of the change process rather than just attribute changes of entities, providing higher-level understanding suitable for construction progress documentation.

## Contributions

- **Surface continuity constraints**: Adds object surface continuity constraints to octree change detection, reducing false positives from noise and improving robustness
- **Semantic change characterization**: Introduces six semantic types to describe change processes (not just attribute changes), enabling cognitive interpretation of transformations
- **Quantization methods**: Proposes specific quantization strategies for different semantic change types to accurately measure entity variations in 3D space
- **Posture alignment**: Develops stable alignment method for symmetric and occluded construction objects, solving orientation estimation challenges
- **Multi-temporal validation**: Demonstrates effectiveness on real construction projects with time-series point clouds

## Method

**Workflow components**:
1. **Octree change detection**: Discretizes 3D space; improved with surface continuity constraints to exclude independently changing cells
2. **Geometric change identification**: Detects spatial differences in voxel occupancy between epochs
3. **Semantic classification**: Maps geometric changes to semantic types (appeared, disappeared, partially disappeared, growth, moved, rotated)
4. **Change quantization**: Applies type-specific metrics (volume change, position offset, rotation angle) to measure magnitude
5. **Posture estimation**: Aligns symmetric objects correctly using stable orientation estimation

**Key technical insight**: Separation between geometric change detection (what changed in space) and semantic interpretation (what does this change mean) enables better handling of construction complexity.

## Datasets & evaluation

- Successfully identifies changing construction entities in complex bridge construction scenarios
- Accurately computes bridge construction progress including demolition/construction phases and equipment repositioning
- Reduces false positives from noise and occlusion compared to standard octree methods
- Semantic descriptions provide interpretable, human-understandable change representations

## Limitations

- How does performance scale with very large construction sites and extended temporal sequences?
- Can the six semantic types adequately represent all construction transformations?
- How sensitive is the method to initial point cloud registration errors?
- Can automated semantic labeling be improved for crowded construction scenes?
- How to handle partial occlusions and incomplete point clouds in dense construction areas?

## Key takeaways

Builds on octree-based point cloud change detection (Park et al. 2021) but adds semantic layer for construction interpretation. Related to voxel and point-based methods but emphasizes semantic description beyond geometric comparison. Complements occupancy-grid approaches with explicit change type categorization.
