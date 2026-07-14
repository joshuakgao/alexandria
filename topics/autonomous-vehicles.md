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

*Last updated: 2026-07-08 | Sources: 6 papers*

## Current thesis

Autonomous vehicle research spans two complementary directions: perception of safety-critical elements and generative world modeling for planning and simulation. On the perception side, the non-parametric memory paradigm and end-to-end mapping frameworks address detecting, tracking, and maintaining maps of the physical environment. On the generative side, GAIA-1 (Hu et al., Wayve, 2023) demonstrates that autoregressive world models trained on large-scale real driving video can generate realistic, controllable driving scenarios — providing synthetic training data and enabling future prediction for planning. The perception line focuses on *what is there now*; the world modeling line focuses on *what might happen next*.

Autonomous vehicle perception of safety-critical elements (construction zones, temporary signs) requires learned spatio-temporal memory that goes beyond single-frame detection. The non-parametric memory paradigm (Bai et al., 2021) establishes three core operations — remember (retain occluded objects), reinforce (strengthen uncertain detections), forget (remove false positives) — learned via continuous convolutions rather than hand-coded Bayesian update rules. 3D occlusion reasoning is the key enabler, distinguishing between "not visible because occluded" and "not visible because it was wrong."

## Key trends

- **Learned spatio-temporal aggregation replaces fixed update rules**: Traditional semantic mapping uses Bayesian fusion with fixed priors; learning the aggregation function via continuous convolutions enables adaptive weighting based on occlusion, distance, and noise — particularly important for small distant objects that require multi-sweep accumulation.
- **Camera-LiDAR fusion for construction zone perception**: Camera provides rich semantic segmentation; LiDAR provides 3D localization and occlusion reasoning. The complementarity is essential for detecting small construction elements at range where either modality alone is insufficient.
- **End-to-end HD map change detection via feature differencing**: Diff-Net (He et al., Baidu, 2021) replaces the traditional multi-step pipeline (object detection → association → comparison) with a single-stage network that computes feature differences between camera images and rasterized HD map projections at multiple scales. The Parallel Cross Difference module directly highlights discrepancies between what the map expects and what the camera observes. ConvLSTM temporal fusion improves robustness for changes consistent across frames. Achieves 76.1% mAP vs. 55.8% baseline, establishing that end-to-end optimization avoids error propagation between isolated pipeline stages.

**2023:**
- **Generative world models for autonomous driving**: GAIA-1 (Hu et al., Wayve, 2023) introduces a multimodal generative world model that combines autoregressive next-token prediction over discretized video (6.5B parameter transformer) with a video diffusion decoder (2.6B params). Trained on 4,700 hours of London driving data, it generates realistic, controllable driving scenarios conditioned on video, text, and action inputs. Demonstrates LLM-like scaling laws for video world models and emergent properties including 3D geometry understanding, reactive agent behaviors, and out-of-distribution generalization. Establishes world modeling as a viable path for generating synthetic training data and enabling future prediction for AV planning.

**2024:**
- **City-scale street view generation via autoregressive video diffusion**: Streetscapes (Deng et al., SIGGRAPH 2024) generates long-range, 3D-consistent street view sequences spanning multiple city blocks, conditioned on overhead maps, height maps, and text prompts (weather, city style). The system uses G-buffer conditioning (semantic labels, disparity, height rendered from maps) for layout and camera control, and a temporal imputation method that prevents quality drift in autoregressive generation. Trained on Google Street View data from 23 cities, it can also interpolate sparse real street view captures to increase frame rate for smooth virtual exploration. Unlike GAIA-1's driving-centric world model (action-conditioned ego-vehicle simulation), Streetscapes focuses on explorable urban environments from arbitrary map layouts — complementary approaches to generative urban scene modeling.

**2026:**
- **Efficient driving world models via motion-appearance decoupling**: MAD (Rahimi et al., CVPR 2026) demonstrates that driving world models can be built by decoupling motion forecasting from appearance synthesis — adapting a single generalist video backbone (SVD or LTX) with two lightweight LoRA adapters rather than massive end-to-end fine-tuning. A Motion Forecaster generates skeletonized pose videos (cars, pedestrians, lanes) capturing multi-agent dynamics, then an Appearance Synthesizer renders photorealistic driving video conditioned on these poses. MAD-SVD matches VISTA quality with <6% of its compute; MAD-LTX 13B matches proprietary Cosmos Predict 2 with 128–700 GPU-hours vs. 25,000–50,000+. The abstract pose intermediate prevents mode collapse and maintains trajectory diversity. Supports text, ego-motion (novel visual sphere representation), and per-object motion controls. This establishes a practical path for the AD community to leverage future advances in generalist video models without prohibitive re-training.

**2025:**
- **Unified end-to-end mapping, localization, and change detection**: RTMap (Du et al., Alibaba/CaiNiao, 2025) is the first framework to jointly solve all three core tasks for HD map maintenance in a single multi-task model. The hybrid query mechanism (prior queries from crowdsourced map + new queries from sensors) with existence-aware matching naturally classifies map elements as matched, outdated, or new — yielding change detection as a byproduct of the matching process rather than a separate pipeline. Per-vertex uncertainty modeling (Laplace distributions) enables both probabilistic localization and noise-aware crowdsourced map fusion. The crowdsourced map becomes a self-evolving memory that improves with each fleet traversal. This represents the convergence of previously independent capabilities (online mapping, localization, change detection) into a unified, deployable system.
- **City-scale benchmarking for 3D map updating**: SceneEdited (Lin et al., U. Adelaide, 2025) introduces the first dataset that bridges change detection and geometric map maintenance — providing outdated point cloud maps, current images, and ground-truth updated maps. Built on Argoverse 2 with 800+ scenes, 73 km of driving, and 23K+ synthesized changes (buildings, poles, overpasses), it reveals that change detection alone is insufficient — the geometric updating step (cross-modality registration, altitude-dependent reconstruction accuracy, FOV limitations) introduces substantial additional challenges. Image-based updating enables crowdsourcing (cameras are ubiquitous) but is fundamentally limited compared to LiDAR re-scanning.

## Open problems

- Multi-agent shared simulation: ShareVerse (Zhu et al., 2026) extends generative world models to multi-agent autonomous driving, enabling distributed vehicles to collaboratively synthesize a consistent shared environment via cross-agent attention and spatiotemporal memory. This opens a path toward scalable multi-vehicle simulation without explicit 3D scene reconstruction.
- Bridging generative world models and planning: GAIA-1 and Streetscapes demonstrate generation but not downstream use for policy learning or planning. MAD demonstrates open-loop planning evaluation (minADE) but no closed-loop driving simulation or downstream policy learning.
- From generated street views to usable simulation: Streetscapes generates visually compelling city-scale walkthroughs but relies on coarse/noisy map data and approximate camera poses; whether generated outputs are accurate enough for training perception or planning systems remains open.
- Closing the loop on decoupled world models: MAD's motion-appearance decoupling is evaluated only in open-loop settings; whether the abstract pose intermediate supports closed-loop planning, reactive simulation, or RL policy learning remains untested
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
