---
title: "Segment and Track Anything"
authors: [Yangming Cheng, Liulei Li, Yuanyou Xu, Xiaodi Li, Yi Yang]
year: 2023
tags: [segmentation, vision-language-models]
url: ""
pdf: "[[2023-samtrack-segment-track-anything.pdf]]"
date_ingested: 2026-06-18
---

# Segment and Track Anything

![[2023-samtrack-segment-track-anything-thumbnail.png]]

## Research gap

SAM-Track extends SAM to video object segmentation by combining interactive annotation (SAM) with temporal tracking (DeAOT). The framework supports two complementary modes: interactive tracking (user provides initial annotations via clicks, boxes, or text) and automatic tracking (detects and tracks new objects appearing throughout the video). Integration with Grounding-DINO enables language-based object selection. SAM-Track achieves strong results on DAVIS benchmarks while supporting practical applications in autonomous driving, medical imaging, and AR.

## Contributions

- **Unified video segmentation framework**: Bridges image-based SAM with video-aware tracking to support temporal coherence
- **Dual tracking modes**: Interactive mode for user-controlled object selection, automatic mode for discovering new objects mid-video
- **Multimodal interaction**: Supports clicks, boxes, strokes, and natural language prompts for flexible object specification
- **Efficient multi-object tracking**: Uses DeAOT's identification mechanism to track multiple objects at minimal speed overhead
- **Grounding-DINO integration**: Enables text-prompted object detection and tracking without manual annotation

## Method

**Interactive Tracking Mode**:
- User provides initial annotations in first frame using SAM (click, box, or text prompts)
- SAM produces high-quality segmentation masks
- DeAOT takes masks as reference annotation and propagates embeddings forward via Gated Propagation Module (GPM)

**Automatic Tracking Mode**:
- Periodically (every n-th frame) applies SAM to segment everything in the frame
- Cross-Modal Refinement (CMR) associates newly detected segments with existing tracks
- Adds new objects to reference frame bank if not already tracked
- Continues propagating both old and new objects

**Fusion Tracking Mode**: Combines interactive and automatic modes for adaptive tracking throughout video.

## Datasets & evaluation

- **DAVIS 2016 Val**: 92.0% J&F (state-of-the-art)
- **DAVIS 2017 Test**: 79.2% J&F
- **Multi-object tracking**: Tracks multiple objects with minimal speed degradation compared to single-object trackers
- **Qualitative**: Demonstrates strong zero-shot performance across diverse domains

## Limitations

- Automatic tracking mode relies on periodic "segment everything" calls; temporal sampling interval (n) requires tuning
- Performance on very fast-moving objects or extreme appearance changes not thoroughly analyzed
- Text-based prompting accuracy depends on Grounding-DINO's capabilities for specific domains
- Computational cost of running SAM periodically for automatic mode not detailed
- Generalization to out-of-domain videos (domain gap between training and deployment)

## Key takeaways

Builds on [[2023-sam-segment-anything|SAM]] for segmentation and DeAOT (AOT-based VOS) for tracking. Precedes SAMURAI which focuses specifically on motion modeling. Complements XMem's long-term memory approach with interactive user control and automatic object discovery. Related to traditional semi-supervised VOS but adds multimodal user control.
