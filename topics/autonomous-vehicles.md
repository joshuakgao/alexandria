---
topic: Autonomous Vehicles
slug: autonomous-vehicles
---

# Autonomous Vehicles

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "autonomous-vehicles")
SORT year DESC
```

## Overview

*Last updated: 2026-06-18 | Sources: 4 papers*

## Current thesis

Autonomous vehicle perception of safety-critical elements (construction zones, temporary signs) requires learned spatio-temporal memory that goes beyond single-frame detection. The non-parametric memory paradigm (Bai et al., 2021) establishes three core operations — remember (retain occluded objects), reinforce (strengthen uncertain detections), forget (remove false positives) — learned via continuous convolutions rather than hand-coded Bayesian update rules. 3D occlusion reasoning is the key enabler, distinguishing between "not visible because occluded" and "not visible because it was wrong."

## Key trends

- **Learned spatio-temporal aggregation replaces fixed update rules**: Traditional semantic mapping uses Bayesian fusion with fixed priors; learning the aggregation function via continuous convolutions enables adaptive weighting based on occlusion, distance, and noise — particularly important for small distant objects that require multi-sweep accumulation.
- **Camera-LiDAR fusion for construction zone perception**: Camera provides rich semantic segmentation; LiDAR provides 3D localization and occlusion reasoning. The complementarity is essential for detecting small construction elements at range where either modality alone is insufficient.
- **End-to-end HD map change detection via feature differencing**: Diff-Net (He et al., Baidu, 2021) replaces the traditional multi-step pipeline (object detection → association → comparison) with a single-stage network that computes feature differences between camera images and rasterized HD map projections at multiple scales. The Parallel Cross Difference module directly highlights discrepancies between what the map expects and what the camera observes. ConvLSTM temporal fusion improves robustness for changes consistent across frames. Achieves 76.1% mAP vs. 55.8% baseline, establishing that end-to-end optimization avoids error propagation between isolated pipeline stages.

**2025:**
- **Unified end-to-end mapping, localization, and change detection**: RTMap (Du et al., Alibaba/CaiNiao, 2025) is the first framework to jointly solve all three core tasks for HD map maintenance in a single multi-task model. The hybrid query mechanism (prior queries from crowdsourced map + new queries from sensors) with existence-aware matching naturally classifies map elements as matched, outdated, or new — yielding change detection as a byproduct of the matching process rather than a separate pipeline. Per-vertex uncertainty modeling (Laplace distributions) enables both probabilistic localization and noise-aware crowdsourced map fusion. The crowdsourced map becomes a self-evolving memory that improves with each fleet traversal. This represents the convergence of previously independent capabilities (online mapping, localization, change detection) into a unified, deployable system.
- **City-scale benchmarking for 3D map updating**: SceneEdited (Lin et al., U. Adelaide, 2025) introduces the first dataset that bridges change detection and geometric map maintenance — providing outdated point cloud maps, current images, and ground-truth updated maps. Built on Argoverse 2 with 800+ scenes, 73 km of driving, and 23K+ synthesized changes (buildings, poles, overpasses), it reveals that change detection alone is insufficient — the geometric updating step (cross-modality registration, altitude-dependent reconstruction accuracy, FOV limitations) introduces substantial additional challenges. Image-based updating enables crowdsourcing (cameras are ubiquitous) but is fundamentally limited compared to LiDAR re-scanning.

## Open problems

- Extending learned memory to general object classes beyond construction elements
- Memory management for long-duration drives without unbounded growth
- Integration of element-level detection with layout-level understanding (lane closures, detour routing)
- Real-time constraints and compute-accuracy trade-offs for deployment

## Contradictions and debates

- **Learned vs. Bayesian aggregation**: Hand-coded Bayesian updates are interpretable and have known convergence properties; learned aggregation is more flexible but less transparent. The optimal balance depends on safety certification requirements.

## Recommended reading order

1. **[[papers/2021-non-parametric-memory-construction-zones|Non-parametric Memory for Construction Zones (2021)]]** — foundational work establishing learned remember/reinforce/forget operations for AV spatio-temporal perception
2. **[[papers/2021-diff-net|Diff-Net (2021)]]** — first end-to-end HD map change detection; feature differencing between camera and rasterized map projections; establishes the map-to-image comparison paradigm
3. **[[papers/2025-rtmap|RTMap (2025)]]** — unified end-to-end framework for mapping + localization + change detection; hybrid queries with existence-aware matching; crowdsourced recursive map improvement; the deployable system perspective
4. **[[papers/2025-sceneedited|SceneEdited (2025)]]** — first city-scale benchmark for 3D PCM updating from images; bridges change detection and geometric map maintenance; reveals that updating is much harder than detection

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
