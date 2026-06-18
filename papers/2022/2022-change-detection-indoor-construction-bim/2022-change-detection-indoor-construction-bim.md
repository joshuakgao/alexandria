---
title: "Change detection for indoor construction progress monitoring based on BIM, point clouds and uncertainties"
authors: [uncertainties Theresa Meyer a, Ansgar Brunn b, Uwe Stilla a]
year: 2022
venue: "Automation in Construction"
tags: [construction-monitoring, change-detection, point-cloud-registration]
url: ""
pdf: "[[2022-change-detection-indoor-construction-bim.pdf]]"
date_ingested: 2026-06-18
---

# Change detection for indoor construction progress monitoring based on BIM, point clouds and uncertainties

![[2022-change-detection-indoor-construction-bim-thumbnail.png]]

## Research gap

This paper tackles the challenging problem of automated construction progress monitoring in confined indoor building spaces using terrestrial laser scanning (TLS) point clouds. Indoor progress monitoring is more complex than outdoor work due to limited views, weak scanning geometries, and occlusions. The authors present a voxel-based change detection method that explicitly models measurement uncertainty using Dempster-Shafer theory of evidence. Rather than treating point clouds as deterministic, they model the belief functions that laser range measurements contribute to spatial occupancy, accounting for sensor uncertainty and stochastic measurement properties. The method compares TLS-acquired point clouds against a reference Building Information Model (BIM), enabling detection of as-built vs. as-planned discrepancies. Results demonstrate that TLS point clouds can verify BIM accuracy up to Level of Accuracy (LOA) 40 when scanning geometry is carefully managed. The approach successfully documents construction progress for real indoor conversion projects, updating BIM status and enabling automated progress tracking with explicit uncertainty quantification.

## Contributions

- **Uncertainty-aware change detection**: Models laser measurement uncertainty using belief functions and Dempster-Shafer theory, moving beyond deterministic point comparisons
- **Voxel-based method**: Discretizes 3D space by voxels and computes occupancy from uncertain measurements
- **BIM verification**: Compares point clouds directly against BIM reference geometry, enabling as-built validation
- **LOA assessment**: Evaluates whether detected changes are significant relative to BIM Level of Accuracy specification
- **Indoor construction focus**: Addresses specific challenges of indoor progress monitoring (occlusions, limited views, weak geometry)

## Method

**Uncertainty modeling**:
1. Model laser range measurement as belief function: probability that a measurement point truly represents occupied space
2. Account for sensor stochastic properties (ranging error, angular resolution)
3. Apply Dempster-Shafer theory to combine belief evidence from multiple measurements
4. Compute voxel occupancy grounded in measurement uncertainty

**Change detection workflow**:
1. Register multi-temporal TLS point clouds to common coordinate system
2. Discretize space into voxels
3. Compute occupancy evidence from point cloud measurements using belief functions
4. Compare occupancy between time epochs, thresholding by LOA specification
5. Identify statistically significant changes (beyond measurement noise)
6. Map changes to BIM objects for progress interpretation

**Key innovation**: Explicit uncertainty modeling prevents false positives from measurement noise and registration errors while enabling metric accuracy evaluation.

## Datasets & evaluation

- **Accuracy validation**: TLS point clouds verified BIM up to LOA 40 (±50 mm) for indoor construction
- **Progress documentation**: Successfully tracked construction progress through multiple temporal epochs
- **Real-world case studies**: Demonstrated on two indoor conversion projects with detailed accuracy assessment
- **Robustness**: Method handles occlusions and limited views through evidence theory framework

## Limitations

- How does performance scale to larger, more complex multi-room indoor environments?
- Can LOA assessment adapt dynamically based on scanning geometry quality?
- How to handle registration errors when they exceed measurement uncertainty?
- Can semantic segmentation improve object-level change interpretation?
- How to reduce reliance on careful scanning geometry planning in practical deployments?

## Key takeaways

Builds on voxel-based change detection foundations (Girardeau-Montaut et al., occupancy grids) but adds rigorous uncertainty quantification. Related to point-based and surface-distance methods but emphasizes probabilistic reasoning over deterministic thresholding. Complements prior BIM-to-point-cloud comparison work by incorporating measurement uncertainty explicitly.
