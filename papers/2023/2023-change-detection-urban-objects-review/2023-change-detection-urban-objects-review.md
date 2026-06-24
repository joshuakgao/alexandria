---
title: "Change detection of urban objects using 3D point clouds: A review"
authors: [Uwe Stilla a, Yusheng Xu a]
year: 2023
venue: "ISPRS Journal of Photogrammetry and Remote Sensing"
tags: [construction-monitoring, change-detection, point-cloud-registration]
url: ""
pdf: "[[2023-change-detection-urban-objects-review.pdf]]"
date_ingested: 2026-06-18
---

# Change detection of urban objects using 3D point clouds: A review

![[2023-change-detection-urban-objects-review-thumbnail.png]]

## Research gap

Comprehensive review of point-cloud-based 3D change detection techniques and their applications in urban environments. The paper surveys critical techniques including data registration, variance estimation, and change analysis, discussing their strengths and limitations. Unlike 2D image-based approaches, 3D point clouds enable geometric analysis without perspective distortion. The review covers multiple data acquisition modalities (LiDAR: ALS, MLS, TLS; photogrammetry: SfM, MVS; SAR) and their respective change detection applications. Key application domains reviewed include land cover/land use monitoring, vegetation surveys, construction automation, building/indoor investigations, and traffic monitoring. The paper identifies significant remaining challenges: dataset reliability and gaps, uncertainty quantification in results, integration of semantics into change detection, and bridging the gap between state-of-the-art techniques and practical engineering demands. The work provides a structured workflow for point-cloud-based change detection and proposes future research directions for robust, automated, and semantically-rich change analysis.

## Contributions

- **Comprehensive technique review**: Surveys registration, variance estimation, and change analysis methods with critical comparison of strengths/weaknesses
- **Multi-modality perspective**: Covers LiDAR (ALS, MLS, TLS), photogrammetry (SfM, MVS), and SAR acquisition for change detection
- **Application-focused taxonomy**: Organizes literature by application domains (vegetation, construction, urban planning, infrastructure monitoring)
- **Gap identification**: Highlights unresolved challenges and research directions for practical deployment
- **Workflow framework**: Provides general workflow for point-cloud change detection with critical decision points

## Method

**General change detection workflow** (three main stages):

1. **Coordinate system alignment** (Registration):
   - Align multi-temporal point clouds to common reference frame
   - Challenges: temporal changes, incompleteness from occlusions, point density variations
   - Techniques: ICP-based, feature-based, hybrid approaches

2. **Spatial and spectral comparison**:
   - Point-based: Cloud2Cloud distance, point-to-surface, surface-to-surface metrics
   - Voxel-based: Occupancy grids, octree hierarchies
   - Segment-based: Object/cluster comparisons

3. **Change representation and analysis**:
   - Uncertainty quantification (occlusions, missing points)
   - Change classification and semantic interpretation
   - Application-specific thresholding and filtering

**Key technical insights**: Choice of registration method, comparison metric, and change representation depends on application-specific requirements (geometry vs. semantics, fine vs. coarse resolution, detection vs. measurement).

## Datasets & evaluation

**Application domains reviewed**:
- **Construction**: Progress monitoring, equipment tracking, structural change detection (97.8% detection rates)
- **Vegetation**: Growth monitoring, deforestation tracking, land cover changes
- **Urban planning**: Building modifications, infrastructure changes, land use transitions
- **Infrastructure**: Road/bridge surface damage, building maintenance, structural health monitoring

**Representative findings**:
- Point clouds enable 3D analysis advantages over 2D: no perspective distortion, true 3D metrics, geometric detail
- Deep learning approaches (CNNs, GNNs) improve change classification but require labeled training data
- Uncertainty modeling critical for robust detection in noisy, incomplete data
- Semantic information increasingly important for interpretation and automation

## Limitations

- How to design reliable benchmark datasets with ground truth labels for change detection validation?
- What is the optimal balance between geometric and semantic change representation for different applications?
- How to quantify and propagate uncertainty through multi-stage pipelines (registration → comparison → interpretation)?
- Can unsupervised learning reduce dependency on annotated training data for change classification?
- How to scale methods to very large spatial and temporal extent (cities over years)?
- How to integrate contextual prior knowledge (e.g., seasonal vegetation changes vs. structural changes)?

## Key takeaways

Extends earlier change detection surveys with focus on 3D point clouds and recent deep learning advances. Complements prior work on specific techniques (registration, occupancy grids) with comprehensive application context. Relates to broader remote sensing and photogrammetry literature while emphasizing 3D-specific considerations.
