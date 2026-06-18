---
title: "SceneEdited: A City-Scale Benchmark for 3D HD Map Updating via Image-Guided Change Detection"
authors: [Chih-hao Lin, Anuj Chin, Sourabh Garg, Ben Dayoub]
year: 2025
tags: [autonomous-vehicles, change-detection, benchmarking]
url: ""
pdf: "[[2025-sceneedited.pdf]]"
date_ingested: 2026-06-18
---

# SceneEdited: A City-Scale Benchmark for 3D HD Map Updating via Image-Guided Change Detection

![[2025-sceneedited-thumbnail.png]]

## Research gap

SceneEdited is the first city-scale dataset explicitly designed for 3D point cloud map (PCM) updating guided by 2D images — bridging the gap between change detection (locating what changed) and map updating (integrating changes into the 3D representation). While prior work stops at change localization, SceneEdited provides the full pipeline: outdated point cloud maps, current RGB images, and ground-truth updated maps, enabling supervised learning for both detection and geometric updating.

## Contributions

- First city-scale dataset for 3D point cloud map updating from images (not just change detection)
- Controlled synthesis of realistic scene changes (additions, removals) on Argoverse 2 data with 23,000+ modified objects
- Open-source toolkit for generating new edited scenes with controllable change types and magnitudes
- Formalization of map updating as a direct sensor-to-map transformation, with both implicit and explicit change detection variants
- Analysis of key challenges: image-to-PCM reconstruction accuracy, registration, FOV limitations for point deletion

## Method

**Dataset Construction:** Built on Argoverse 2 LiDAR data. Dynamic objects are filtered using 3D annotations. Static point clouds are voxelized at 5cm resolution. Outdated versions are created by: (1) automatically removing static objects using bounding box annotations, (2) inserting new objects from other scenes or synthetic sources, (3) manually editing complex structures (buildings, overpasses). Change masks are automatically generated from the difference between updated and outdated scenes.

**Map Updating Formulation:**
- *Implicit:* P_upd = MapUpdate(P_out, I_curr) — end-to-end, no explicit change detection required
- *Explicit:* P_upd = MapUpdate(P_out, I_curr, C_curr) where C_curr = ChangeDetect(P_out, I_curr) — change masks guide selective updating

**Baselines:**
- *Point Addition:* COLMAP SfM from current images → dense reconstruction → registration to outdated map → geometric fusion
- *Point Deletion:* Ground-truth change masks + FOV check → remove outdated points visible in current images; points outside camera FOV conservatively retained

**Evaluation Metrics:** Chamfer distance and Hausdorff distance between estimated and ground-truth updated point clouds, measured per-scene and per-object.

## Datasets & evaluation

- SfM-based point addition achieves reasonable reconstruction for nearby structures but accuracy degrades with altitude (tall buildings, overpasses) due to limited camera viewpoint diversity
- Point deletion is fundamentally limited by camera field of view — points not observed in current images cannot be confidently removed
- Cross-modality registration (image-derived points ↔ LiDAR map) is a significant challenge; misalignment introduces artifacts
- The dataset reveals that change detection alone is insufficient — the geometric updating step introduces substantial additional challenges not addressed by prior CD benchmarks

## Limitations

- Synthesized changes (object insertion/removal) may not fully capture real-world change complexity (gradual construction, partial occlusion during modification)
- Image-based map updating is fundamentally limited by camera FOV and reconstruction accuracy — LiDAR-based updating may be necessary for high-precision applications
- Altitude-dependent accuracy degradation limits applicability to tall structures seen from street-level cameras
- No temporal reasoning — each update is treated independently; sequential updates over time not modeled
- Evaluation metrics (Chamfer, Hausdorff distance) treat all points equally; importance-weighted metrics (lane markings more critical than distant buildings) not explored
- The toolkit enables automatic scene editing but the realism of automatically generated changes needs further validation

## Key takeaways

The dataset covers 73 km of driving across ~3 km² of urban area with 800+ up-to-date scenes, 2,000+ synthesized out-of-date versions, and 23,000+ changed objects (buildings, overpasses, poles, roadside infrastructure). Changes are created both manually (complex removals like buildings) and automatically (controlled object insertion/deletion), simulating realistic urban evolution. Each scene includes calibrated RGB images, LiDAR scans, and voxelized point cloud maps with detailed change masks.

The paper formulates map updating as: given an outdated PCM P_out and current geo-referenced images I_curr, estimate an updated map P_upd that reproduces current scene geometry. This can operate end-to-end (implicitly learning where changes occurred) or with an explicit change detection module that provides change masks to guide the update. Baseline methods use COLMAP-based structure-from-motion for point addition and image-guided deletion within camera field-of-view. Key challenges identified include reconstruction accuracy from images, cross-modality registration (image-derived points ↔ LiDAR map), and altitude-dependent accuracy degradation.

SceneEdited fills a critical gap in the dataset landscape: prior CD datasets (Urb3DCD, Change3D, SLPCCD) support change localization but not map updating; prior HD map datasets (Argoverse TbV) support semantic-layer changes but not PCM geometry. Diff-Net detects HD map changes from images but doesn't update the 3D map. The non-parametric memory paper (Bai et al.) accumulates 3D evidence for construction elements but doesn't address full map geometry updating. SceneEdited connects change detection to map maintenance by providing the complete input-output pipeline (outdated map + current images → updated map). From the University of Adelaide group, building on Argoverse 2 infrastructure.
