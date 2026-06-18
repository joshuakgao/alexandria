---
title: "Semantics-aided 3D change detection on construction sites using UAV-based photogrammetric point clouds"
authors: [Rong Huang, Dong Xu, Ludwig Hoegner, Uwe Stilla]
year: 2022
venue: "Automation in Construction"
tags: [construction-monitoring, change-detection, segmentation, remote-sensing]
url: ""
pdf: "[[2022-semantics-aided-3d-change-detection-construction.pdf]]"
date_ingested: 2026-06-18
---

# Semantics-aided 3D change detection on construction sites using UAV-based photogrammetric point clouds

![[2022-semantics-aided-3d-change-detection-construction-thumbnail.png]]

## Research gap

This paper presents a semantic-aided change detection framework for construction progress monitoring using UAV-based photogrammetric point clouds. The key innovation is combining geometric change detection with semantic segmentation to achieve object-level change interpretation. The framework operates in two complementary stages: (1) occupancy-based spatial difference identification detecting geometric changes (appearance/shape modifications) while implicitly handling occlusions, and (2) semantic-aided change analysis leveraging segmentation class probabilities to detect changes not captured by geometry alone. Dempster-Shafer theory fuses multi-temporal information, quantifying uncertainty in both geometric and semantic changes. Unlike purely geometric approaches that struggle with incomplete data, semantic information enables object-level interpretation. Results on real construction sites show 97.8% detection accuracy for one construction period and 93.6% for another, successfully identifying distinct geometric and semantic change characteristics. The method addresses fundamental challenges of construction monitoring: handling occlusions, distinguishing significant changes from measurement noise, and achieving high-level semantic understanding.

## Contributions

- **Dual-stage change detection**: Combines geometric (occupancy-based) and semantic change detection in coordinated framework
- **Occupancy-aware occlusion handling**: Implicitly addresses occlusions by reasoning about viewing rays and belief assignments
- **Uncertainty quantification**: Uses Dempster-Shafer theory for rigorous fusion of multi-temporal evidence with uncertainty estimates
- **Semantic-level interpretation**: Enables object-level analysis beyond pixel/voxel comparisons through semantic segmentation
- **UAV-based validation**: Demonstrates effectiveness on real construction sites using practical UAV acquisition

## Method

**Two-part change detection framework**:

**Part 1: Occupancy-based spatial difference identification**
- Discretize 3D space using voxels
- Model sensor viewing rays from image metadata
- Detect occupancy conflicts: discrepancies between expected and actual space occupancy
- Implicitly handles occlusions by reasoning about visibility from camera positions
- Identifies geometric changes: appearance or shape modifications of building objects

**Part 2: Semantic-aided change analysis**
- Perform semantic segmentation of point clouds producing class probability maps
- Detect semantic changes: object class transitions between time epochs
- Quantify semantic uncertainty using probability distributions
- Identify changes where occupancy-based methods alone are insufficient (e.g., object state changes without geometry modification)

**Information fusion**:
- Apply Dempster-Shafer evidence theory to combine geometric and semantic evidence
- Assign belief masses based on measurement confidence
- Aggregate multi-temporal observations with uncertainty tracking

**Key insight**: Geometric changes alone cannot fully characterize construction progress; semantic information about object types and states is essential for high-level interpretation.

## Datasets & evaluation

- **Real construction site 1**: 97.8% detection of changed areas (Dec 12, 2014 – Jan 16, 2015)
- **Real construction site 2**: 93.6% detection accuracy (Jan 16, 2015 – Feb 26, 2015)
- **Change characterization**: Correctly identified both geometric changes (shape, position) and semantic changes (material, classification)
- **Occlusion handling**: Method robustness demonstrated despite common occlusions in construction environments
- **Object-level insights**: Semantic approach enabled interpretation of temporary construction objects vs. permanent structures

## Limitations

- How does performance scale to large, multi-phase construction projects?
- Can semantic segmentation be improved for domain-specific construction object categories?
- How to distinguish construction-related changes from temporary objects (equipment, debris)?
- What is sensitivity to registration errors between temporal epochs?
- Can the method adapt to different UAV flight patterns and acquisition geometries?
- How to reduce computational cost for real-time or near-real-time progress monitoring?

## Key takeaways

Builds on occupancy-grid approaches for change detection (Girardeau-Montaut, robotic applications) and point-based methods. Advances prior construction monitoring work by adding semantic layer; related to general scene understanding approaches but specialized for construction context. Uses Dempster-Shafer evidence theory (Meyer et al., uncertainty in point clouds) for rigorous probabilistic reasoning.
