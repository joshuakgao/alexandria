---
title: "Chamelion: Reliable Change Detection for Long-Term LiDAR Mapping in Transient Environments"
authors: [Hyungtae Jang, Jaehyun Lee, Deva Nahrendra, Hyun Myung]
year: 2026
venue: "RA-L 2026"
tags: [change-detection, 3d-scene-understanding, autonomous-vehicles]
url: ""
pdf: "[[2026-chamelion.pdf]]"
date_ingested: 2026-06-18
---

# Chamelion: Reliable Change Detection for Long-Term LiDAR Mapping in Transient Environments

![[2026-chamelion-thumbnail.png]]

## Research gap

Chamelion addresses online low-dynamic (LD) change detection in 3D LiDAR data — structural modifications that persist across sessions but differ from the prior map (e.g., construction site reconfiguration, furniture rearrangement). Unlike high-dynamic changes (pedestrians, vehicles) that are well-studied, LD changes are harder because they require comparing sparse current scans against dense prior maps while handling pervasive occlusion.

## Contributions

- Composition-based data augmentation generating pseudo change labels from single-session LiDAR scans, eliminating multi-session data collection
- Dual-head architecture separating change classification (high-level features) from cross-visibility confidence estimation (low-level features) for occlusion-aware detection
- Exponential decay confidence model based on nearest-neighbor distance between map and scan points
- Probabilistic Bayesian map update conditioned on cross-visibility confidence, enabling reliable long-term map maintenance
- Cross-environment generalization: trained on one environment, tested on construction sites and indoor offices

## Method

**Data augmentation**: From a single LiDAR session, extract static objects via multi-object tracking. Store object snapshots (point clouds across frames) in a database. Paste single-frame snapshots into scans (positive changes) and full snapshot sequences into maps (negative changes). Ground segmentation ensures plausible placement.

**Network**: Input is a concatenated 4D sparse tensor of map points (ν=0) and scan points (ν=1) with (x, y, z, ν) representation. 4D sparse CNN (MinkowskiEngine) extracts features. Dual heads:
- *Class head*: Predicts static/positive/negative per voxel using cross-entropy loss on high-level backbone features
- *Confidence head*: Predicts cross-visibility score c(d) using MSE loss on low-level features; ground truth defined by exponential decay of nearest-neighbor distance between map/scan points, with thresholds τ_vox and τ_ocl

**Map update**: Recursive Bayesian filter integrates observations over time. Only points with high confidence (visible in both map and scan) contribute to the log-odds update; low-confidence (occluded) points are reset to prior.

## Datasets & evaluation

| Dataset | Method | Scan-wise IoU↑ | Map PR↑ | Map RR↑ | Map F1↑ |
|---------|--------|---------------|---------|---------|---------|
| Custom (Lab) | Chamelion | **0.767** | 0.955 | 0.713 | 0.816 |
| Custom (Lab) | MapMOS | 0.757 | 0.992 | 0.166 | 0.284 |
| Custom (Const-2F) | Chamelion | **0.484** | 0.954 | 0.839 | **0.893** |
| LiSTA (Simu-2) | Chamelion | **0.764** | 0.987 | — | — |

Key observations:
- Geometric methods (visibility, occupancy) have high false positive rates due to occlusion sensitivity
- MapMOS achieves good scan-wise IoU but poor map-wise RR because it only classifies where scan points exist
- Chamelion's cross-visibility confidence enables map updates in occluded regions where other methods fail

## Limitations

- Cross-visibility is learned from pseudo-labeled data with fixed assumptions about registration error and voxel size — performance may degrade under varying registration quality
- Limited to structural/geometric changes detectable in LiDAR — cannot detect appearance-only changes (color, texture)
- Currently handles only two relation types (on/uphold, in/contain) for spatial relationships
- Map-wise rejection rate (RR) is sometimes lower than geometric baselines on datasets with limited scan coverage (LiSTA), where the confidence head is overly conservative
- No explicit object-level change tracking — operates at the point/voxel level unlike POCD's object-level Bayesian updates
- Generalization tested across construction sites and offices; applicability to outdoor large-scale environments (driving, urban) not evaluated

## Key takeaways

The framework has three key innovations. First, a **composition-based data augmentation** strategy generates training data from single-session LiDAR scans: static objects are extracted via multi-object tracking, stored in a database, and pasted into arbitrary locations in scans and maps to create pseudo positive/negative changes. This eliminates the need for costly multi-session data collection. Second, a **dual-head network** built on a 4D sparse CNN backbone (MinkowskiEngine) jointly predicts change class (static/positive/negative) and cross-visibility confidence (likelihood that a point is visible in both map and scan). The class head uses high-level features for semantic understanding, while the confidence head uses lower-level geometric features for local occlusion estimation. Third, a **probabilistic Bayesian update** integrates change detections over multiple observations, conditioned on confidence scores, to maintain long-term map consistency.

Evaluated on real-world construction sites and indoor offices (custom dataset + LiSTA benchmark), Chamelion achieves the best scan-wise IoU (0.728–0.767 on custom, 0.621–0.764 on LiSTA) while maintaining high map preservation rates (>95% PR). The method generalizes across diverse environments without per-dataset retraining — trained on one custom environment and tested on different construction sites and the LiSTA office dataset.

Extends **POCD**'s (2022) probabilistic object-level stationarity tracking from TSDF-based to learning-based change detection. Both address semi-static/transient environments and use Bayesian updates for map maintenance, but Chamelion replaces geometric TSDF differences with learned features and adds explicit occlusion modeling via the confidence head. Complements the visual/RGB change detection methods in this topic (SceneDiff, O-SCD, 3DGS-CD) by operating in the **LiDAR point cloud** domain — structurally different from image-based approaches but addressing the same fundamental problem of detecting what changed between observations. The composition-based data augmentation shares philosophy with **Changen2**'s synthetic change generation (both create training data from single observations to avoid multi-temporal collection), but operates in 3D point clouds rather than 2D images. Unlike SceneDiff's training-free pipeline, Chamelion requires supervised training but achieves real-time online operation.
