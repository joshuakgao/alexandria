---
title: "Perspective Fields for Single Image Camera Calibration"
authors: [Linyi Jin, Jianming Zhang, Yannick Hold-Geoffroy, Oliver Wang, Kevin Blackburn-Matzen, Matthew Sticha, David Fouhey]
year: 2023
venue: "CVPR"
tags: [camera-calibration]
url: ""
pdf: "[[2023-perspective-fields.pdf]]"
date_ingested: 2026-06-18
---

# Perspective Fields for Single Image Camera Calibration

![[2023-perspective-fields-thumbnail.png]]

## Research gap

Perspective Fields proposes a per-pixel representation of camera perspective that avoids the assumptions and failure modes of traditional single-image camera calibration. Instead of predicting a single set of global camera parameters (roll, pitch, FoV) that assume a centered principal point, Perspective Fields predicts two dense fields for every pixel: an **Up-vector** (the projection of the gravity-aligned up direction) and a **Latitude** value (the angle between the incoming light ray and the horizontal plane). This local, non-parametric representation is invariant to cropping, equivariant to rotation, and works across multiple camera projection models (perspective, fisheye, equirectangular).

## Contributions

- Perspective Fields: a per-pixel, non-parametric camera representation (Up-vector + Latitude) that makes no assumptions about camera model or principal point location
- Invariance to cropping, equivariance to rotation — critical for images in the wild that are commonly edited
- PerspectiveNet: SegFormer-based pixel-to-pixel predictor trained on 360° panorama crops; generalizes across indoor, outdoor, natural, and synthetic scenes
- ParamNet: lightweight ConvNet that extracts traditional camera parameters from Perspective Fields when needed
- Perspective Field Discrepancy (PFD): compositing metric that correlates with human perception of perspective mismatch better than horizon-line metrics
- Teacher-student distillation for object cutout calibration using COCO with pseudo labels

## Method

**Perspective Fields Definition**: For each pixel x, Up-vector u_x is the projection of the gravity-aligned up direction onto the image plane (Eq. 1). Latitude φ_x is the angle between the light ray and the horizontal plane (Eq. 2). Both are defined for arbitrary projection functions P(X), not just pinhole models.

**PerspectiveNet**: SegFormer MiT-B3 encoder with two decoder heads outputting per-pixel probability distributions over discretized Up-vector angles and Latitude bins. Trained with cross-entropy loss on crops from 192k panoramas (indoor + natural + street views) with random roll [-45°,45°], pitch [-90°,90°], FoV [30°,120°]. Data augmentation: color jitter, blur, horizontal flip, rotation, cropping.

**ParamNet**: ConvNeXt-tiny takes stacked Up-vector + Latitude predictions as input, outputs camera parameters (roll, pitch, FoV, optionally principal point). Trained with L1 loss.

**PFD Metric**: E_PFD = λ·arccos(u1·u2) + (1-λ)·|l1-l2|, averaged over all pixels. λ=0.5. Used for compositing quality assessment.

**Object Distillation**: Scene-level PerspectiveNet generates pseudo labels on COCO images. Object crops + pseudo Perspective Fields form training pairs for an object-level model, with random background removal (70%) for domain adaptation.

## Datasets & evaluation

Scene-level Perspective Fields: Stanford2D3D — 2.18° mean Up error (vs. 3.58° Percep., 7.39° CTRL-C), 3.40° mean Latitude error. TartanAir — similar relative improvements. On cropped images: 2.21° Up error (vs. 5.78° Percep., 8.52° CTRL-C) — demonstrating robustness to principal point shifts. Camera parameter recovery via ParamNet: reduces pitch error by 40% on cropped images vs. CTRL-C. User study: PFD correlates more strongly with human perspective perception than horizon-line-based metrics (2.3× higher Spearman correlation). Object cutouts: distilled model predicts meaningful Perspective Fields for segmented objects.

## Limitations

- Primarily evaluated on perspective projection; generalization to fisheye and other lens models is demonstrated but not deeply evaluated
- ParamNet still assumes a parametric camera model for final output — the non-parametric Perspective Fields advantage is partially lost when converting to parameters
- Training on panorama crops may not cover all in-the-wild image distributions (e.g., extreme close-ups, microscopy)
- The discretized output (classification over bins) introduces quantization error; regression approaches might be more precise for fine-grained applications
- Object-level distillation depends on the quality of the scene-level model's pseudo labels
- No evaluation on video sequences where temporal consistency of Perspective Fields could be important

## Key takeaways

The system uses a SegFormer (MiT-B3) encoder with two decoder heads to predict discretized Up-vector and Latitude distributions per pixel, trained on crops from 360° panoramas where ground truth is easily derived. When traditional camera parameters are needed, a separate ConvNet (ParamNet) extracts roll, pitch, FoV, and principal point from the predicted Perspective Fields. The paper also proposes Perspective Field Discrepancy (PFD), a metric for measuring perspective consistency between foreground and background images during compositing, which correlates more strongly with human perception than horizon-line-based metrics.

On Stanford2D3D and TartanAir, PerspectiveNet achieves 2.18° mean Up error and 3.40° mean Latitude error — roughly halving the error of prior methods. On cropped images (off-center principal point), it reduces pitch error by 40% over CTRL-C. A distillation variant trained on COCO object cutouts extends the approach to object-centric images for compositing.

Perspective Fields fundamentally reframes camera calibration from a global parameter estimation problem to a dense per-pixel prediction problem — analogous to how depth estimation moved from single-value prediction to dense per-pixel maps. Prior methods (Hold-Geoffroy et al., CTRL-C, UprightNet) assume centered principal points and single pinhole models, which fail on cropped/edited images in the wild.

The per-pixel formulation connects to broader trends in visual geometry: π³ (3d-scene-reconstruction topic) predicts geometry in each frame's own coordinate system for similar robustness benefits, and Depth Anything predicts per-pixel depth. Perspective Fields could complement these as an additional geometric signal — providing camera-intrinsic information that depth estimation assumes is known.
