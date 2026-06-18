---
title: "Diff-Net: Image Feature Difference based High-Definition Map Change Detection for Autonomous Driving"
authors: [Ye He, Lingxi Jiang, Zehao Liang, He Wang, Tianjia Song]
year: 2021
venue: "ICRA"
tags: [autonomous-vehicles, change-detection]
url: ""
pdf: "[[2021-diff-net.pdf]]"
date_ingested: 2026-06-18
---

# Diff-Net: Image Feature Difference based High-Definition Map Change Detection for Autonomous Driving

![[2021-diff-net-thumbnail.png]]

## Research gap

Diff-Net is the first end-to-end deep neural network for high-definition (HD) map change detection in autonomous driving. Instead of the conventional multi-step pipeline (object detection → element association → difference calculation), Diff-Net directly infers map changes by comparing features extracted from online camera images against rasterized HD map projections in a single-stage network.

## Contributions

- First end-to-end network for HD map change detection, replacing multi-step pipeline (detection → association → comparison) with a single-stage learning-based solution
- Parallel Cross Difference (PCD) module that computes multi-scale feature differences between camera images and rasterized HD map projections
- Rasterized map representation: projecting HD map elements onto camera view images, creating a DNN-consumable representation of prior map knowledge
- ConvLSTM-based spatio-temporal fusion for temporal consistency across video frames
- Three datasets (synthetic single-frame, synthetic video, real-world) for comprehensive validation

## Method

**Input Representation:** HD map elements within a region of interest are projected onto the camera image plane using the camera pose (from localization) and intrinsic calibration. Projected areas are filled with homochromatic colors by object type, producing a binary/multi-class rasterized image.

**Parallel Feature Extraction:** Two CNN backbones — a shallow 11-layer CNN for the clean rasterized images and a deeper backbone for camera images — extract pyramid features at three scales (76×76, 38×38, 19×19).

**Parallel Cross Difference (PCD):** At each scale, features from both branches are differenced to highlight discrepancies between what the map says should be there and what the camera actually observes. Feature propagation (FP) modules share information across scales.

**Feature Decoder:** Anchor-based detection head (YOLOv3-style) predicts bounding boxes with change categories (to_add, to_del, correct) from the decoded difference features.

**Spatio-Temporal Fusion:** ConvLSTM module fuses features from past frames into the current frame's representation, improving robustness for consistent changes visible across multiple frames.

## Datasets & evaluation

- **Synthetic datasets:** Diff-Net achieves 60.5% mAP (single-frame) and 76.1% mAP (video with ConvLSTM) vs. 43.3%/55.8% for YOLOv3+association baseline
- **Real-world dataset (R-VSCD):** 81.0% top-1 accuracy for change classification vs. 55.8% baseline and 72.5% Diff-Net without temporal fusion
- **Feature visualization:** PCD module outputs show accurate attention on changed map elements across scales, confirming the network learns meaningful feature differences rather than just object detection
- End-to-end optimization avoids error propagation between isolated pipeline stages (detection errors → association errors → false change detections)

## Limitations

- Focused only on traffic signals (regular-shaped objects); extension to irregularly shaped elements (lane markings, road boundaries) acknowledged as future work
- HD map changes are rare events in practice; synthetic data augmentation is necessary but sim-to-real gap exists
- Requires accurate camera localization (global pose) for map projection; localization errors cause misalignment in the rasterized image
- The ConvLSTM temporal module operates on a fixed number of past frames; longer temporal reasoning not explored
- Single camera view only; multi-camera or LiDAR fusion could improve coverage and robustness
- Change detection is binary per-element; does not estimate the severity or urgency of changes for prioritized map updates

## Key takeaways

The key design is a parallel feature difference calculation structure: HD map elements are projected onto the camera view as rasterized images, then two parallel CNN backbones extract pyramid features from both the camera and rasterized images at multiple scales. A Parallel Cross Difference (PCD) module computes feature differences between the two modalities, and a feature decoder produces change detection results as bounding boxes with three change categories: "to add" (missing from map), "to del" (should be removed from map), and "correct" (map is accurate). A ConvLSTM-based spatio-temporal fusion module aggregates temporal information across consecutive frames for improved robustness.

Evaluated on synthetic and real HD map change datasets (traffic signals in Chinese cities), Diff-Net achieves 76.1% mAP with spatio-temporal fusion vs. 55.8% for the conventional YOLOv3+association baseline, and 81.0% top-1 accuracy on real-world changes.

Diff-Net connects the HD map maintenance problem with scene change detection techniques. Unlike traditional change detection that compares image pairs or 3D models, Diff-Net compares camera observations against a prior HD map representation — a map-to-image change detection formulation. The rasterized map projection approach draws from recent planning/prediction works that render map elements as images. Compared to Pannen et al. (crowd-based particle filter for map changes) and Heo et al. (adversarial pixel-level detection), Diff-Net provides the first end-to-end anchor-based approach with explicit change categorization. From Baidu's autonomous driving group. The feature difference approach anticipates later work on change detection in the broader computer vision community.
