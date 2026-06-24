---
title: "RTMap: Real-Time Recursive Mapping with Change Detection and Localization"
authors: [Yuheng Du, Sheng Yang]
year: 2025
venue: "ICCV 2025"
tags: [autonomous-vehicles, change-detection, visual-localization]
url: ""
pdf: "[[2025-rtmap.pdf]]"
date_ingested: 2026-06-18
---

# RTMap: Real-Time Recursive Mapping with Change Detection and Localization

![[2025-rtmap-thumbnail.png]]

## Research gap

RTMap is the first end-to-end framework that simultaneously solves three core challenges for autonomous driving HD map maintenance in real time: (1) uncertainty-aware online HD mapping, (2) probabilistic map-based localization, and (3) real-time structural change detection — all within a single multi-task architecture deployed onboard. The crowdsourced map serves as a self-evolving memory that improves with each traversal.

## Contributions

- First end-to-end framework jointly solving prior-aided online HD mapping, map-based localization, and change detection for multi-traversal crowdsourced map maintenance
- Hybrid query mechanism with existence-aware matching that naturally differentiates matched, outdated, and new map elements from a single decoder
- Uncertainty-aware map element modeling (per-vertex Laplace distributions) enabling probabilistic localization and noise-aware crowdsourced map fusion
- Crowdsourcing pipeline that recursively improves map accuracy and freshness across multi-agent traversals

## Method

**Hybrid Queries:** Two query sources — Q_prior (encoded from prior-map elements as fixed-point instances with XOY coordinates + one-hot categories) and Q_new (zero-padded for detecting new elements). Combined with hierarchical query embeddings: Q_hybrid = {Q_map, Q_fake, Q_new} + Q_hie.

**Existence-Aware Matching:** During training, ground-truth change events pre-attribute Q_map predictions to existing elements; remaining elements matched to Q_new via Hungarian matching. Q_fake (no pre-attribution) develops low confidence scores, enabling separation from Q_map during inference via confidence thresholding.

**Uncertainty Modeling:** Per-vertex Laplace distributions model geometric uncertainty of map elements. NLL loss enables joint learning of position and uncertainty. Used downstream for: (1) weighted state estimation in localization, (2) noise-aware fusion during crowdsourcing.

**Localization:** Two options — (1) E2E pose head T^E (end-to-end learned), (2) optimization-based state estimator T^R using inferred correspondences D_t between matched elements. T^R enables optional tight coupling with inertial measurements.

**Crowdsourcing:** Cloud service fuses multi-traversal local HD maps using uncertainty-weighted averaging, filtering outdated elements via change detection outputs.

## Datasets & evaluation

- **Mapping:** Prior-aided online HD map quality consistently improves with crowdsourcing cycles; uncertainty-aware fusion yields cleaner maps than naive stacking
- **Localization:** Centimeter-level accuracy; optimization-based T^R outperforms E2E T^E; both outperform standalone localization methods (EgoVM, BEV-Locator)
- **Change Detection:** Effective identification of matched/outdated/new elements; confidence-based separation of Q_map and Q_fake validated
- **Multi-dataset:** Evaluated on Argoverse 2 and nuScenes; generalizes across sensor configurations
- **Real-time:** Onboard deployment with end-to-end inference, suitable for consumer-grade hardware

## Limitations

- Focused on vectorized semantic map elements (lanes, boundaries); does not address point cloud map (PCM) geometry updating (addressed by SceneEdited)
- Crowdsourcing assumes consistent sensor quality across agents; heterogeneous fleets (different cameras, calibrations) not explicitly handled
- The confidence-based separation of Q_map and Q_fake during inference may struggle with subtle or gradual changes that don't produce strong confidence differences
- Evaluated on public datasets with simulated multi-traversals; real-world fleet-scale crowdsourcing validation is needed
- Change detection is element-wise (insertion/deletion); fine-grained changes (lane width modification, marking updates) not explicitly modeled
- Scalability of the cloud crowdsourcing service to city-scale or national-scale HD map maintenance is untested

## Key takeaways

The key innovation is a hybrid query mechanism that processes two types of queries — prior queries (from the existing crowdsourced map) and new queries (from current sensor observations). Through an existence-aware matching scheme, prior queries are differentiated during training into matched queries (Q_map, confirmed to still exist) and fake queries (Q_fake, outdated elements), while new queries (Q_new) detect previously unseen elements. This three-way classification naturally yields map change detection (fake = deleted, new = added) while the matched elements provide correspondences for localization.

RTMap also outputs per-vertex uncertainty estimates (Laplace distributions) for all map elements, enabling both probabilistic state estimation for localization and noise-aware multi-traversal map fusion during crowdsourcing. A cloud service asynchronously stitches crowdsourced online HD maps from multiple agents into a global prior-map for subsequent traversals. Experiments on Argoverse 2 and nuScenes demonstrate strong performance on mapping, localization (centimeter-level), and change detection simultaneously.

RTMap unifies capabilities that prior work addressed independently: MapTR/MapEX for online HD mapping, EgoVM/BEV-Locator for map-based localization, ExelMap for change detection. Unlike Diff-Net (which compares camera vs. rasterized map), RTMap operates on vectorized map elements with learned correspondences. Unlike the non-parametric memory paper (which accumulates 3D evidence for construction elements), RTMap maintains a full vectorized HD map with crowdsourced recursive updates. Unlike SceneEdited (which benchmarks PCM-level updating), RTMap provides a deployable end-to-end system for semantic map layer maintenance. From Alibaba/CaiNiao's autonomous driving group, designed for practical fleet deployment.
