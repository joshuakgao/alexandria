---
title: "Boxer: Robust Lifting of Open-World 2D Bounding Boxes to 3D"
authors: [Daniel DeTone, Wei-Chiu Shen, Zander Zhang, Fangyin Ma, Julian Straub, Richard Newcombe, Jakob Engel]
year: 2026
tags: [3d-scene-understanding]
url: ""
pdf: "[[2026-boxer-lifting-2d-to-3d.pdf]]"
date_ingested: 2026-06-18
---

# Boxer: Robust Lifting of Open-World 2D Bounding Boxes to 3D

![[2026-boxer-lifting-2d-to-3d-thumbnail.png]]

## Research gap

Boxer addresses the open-world 3D object detection problem by decomposing it into two stages: leveraging powerful open-vocabulary 2D detectors (DETIC, OWLv2, SAM3) to localize objects in 2D, then lifting those detections into metric 3D space. The core contribution is BoxerNet, a transformer-based network that predicts metric 3D bounding boxes from 2D proposals paired with optional depth information (sparse point clouds or dense depth maps). The algorithm fuses multi-view detections across video sequences with geometric filtering to produce globally consistent, de-duplicated 3D bounding boxes in metric world space.

## Contributions

- Two-stage decomposition: leverage mature open-world 2D detectors + dedicated 3D lifting model
- BoxerNet: transformer architecture extending CuTR with aleatoric uncertainty head and median depth patch encoding for flexible depth inputs
- Multi-view temporal fusion: robust de-duplication and global consistency across video sequences
- Large-scale training: 1.2M+ unique 3DBBs across multiple camera models and depth modalities
- Strong empirical results: 0.532 mAP on egocentric data without depth (vs. 0.010 CuTR), 0.412 mAP on CA-1M with depth (vs. 0.250 CuTR)

## Method

**Input:** Posed, calibrated video frames with camera intrinsics/extrinsics, optional sparse point clouds or dense depth maps, and per-frame 2D detections from open-world detectors.

**BoxerNet architecture:** Transformer-based model that:
1. Encodes 2D bounding box proposals (coordinates, size, confidence) alongside image features
2. Optionally encodes depth as median-pooled patches (supports both sparse and dense inputs)
3. Regresses 3D box center, dimensions, and rotation in metric world space
4. Predicts aleatoric uncertainty for robust regression via learned variance per prediction

**Post-processing:** Multi-view temporal fusion operates on detected 3D boxes across the video sequence:
1. Geometric filtering uses camera projection to identify consistent detections across views
2. Median voting across frames and cameras produces final globally consistent 3DBBs
3. De-duplication removes redundant detections of the same object

The architecture is agnostic to camera model (pinhole, fisheye) and depth modality through flexible conditioning, enabling training on heterogeneous datasets.

## Datasets & evaluation

- **Egocentric settings (without depth):** 0.532 mAP vs. 0.010 mAP for CuTR baseline
- **CA-1M benchmark (with depth):** 0.412 mAP vs. 0.250 mAP for CuTR baseline
- Strong open-world category coverage: evaluated on spice jar, hairdryer, sink drain, TV remote, and many other long-tail objects
- Robust to camera type variation: trained across multiple device types with different intrinsics and distortion models
- Flexible depth handling: maintains performance whether sparse point clouds or dense depth maps are available

## Limitations

- **Monocular performance:** While egocentric results are strong, absolute monocular 3D localization (without depth or multi-view) remains inherently ambiguous and relies on dataset priors
- **Depth sensor availability:** Performance ceiling likely bounded by quality of available depth; sparse SLAM/SfM point clouds provide less signal than dense RGB-D
- **Dynamic objects:** Algorithm estimates static 3D boxes; handling articulated or moving objects requires additional reasoning
- **Small/occluded objects:** Multi-view fusion helps but geometric ambiguity remains for objects visible in only one or two frames
- **Real-time performance:** Not discussed; inference cost for BoxerNet + fusion pipeline unclear for deployment scenarios

## Key takeaways

The key insight is that this decoupling enables the model to focus on the geometric lifting problem rather than 2D detection, reducing demand for expensive 3DBB annotations while inheriting the semantic coverage and broad category support of web-scale 2D models. Boxer is trained on over 1.2 million unique 3D bounding boxes across multiple device types (pinhole and fisheye cameras) with varying depth modalities, making it robust to different input configurations.

Boxer builds on CuTR [[2024-cu-tr-to-3d]] which first tackled 2D-to-3D lifting but focused on egocentric video with constraints on depth input. Boxer extends CuTR by: (1) supporting variable depth modalities via median patch encoding, (2) adding aleatoric uncertainty for robust regression, (3) scaling training 6x larger with 1.2M annotations.

Differs from related open-world 3D detection approaches (3D-MOOD, DetAny3D, OVMono3D) which operate monocularly and cannot effectively leverage depth when available. SAM3D provides richer output (full shape + texture) but lacks depth conditioning and multi-view fusion.

Complements EgoLifter and ConceptGraphs which lift 2D segmentation masks to 3D; Boxer focuses on efficient bounding box detection with explicit temporal consistency.
