---
title: "Changes in Real Time: Online Scene Change Detection with Multi-View Fusion"
authors: [Chamuditha Jayanga Galappaththige, Jason Lai Lloyd Windrim, Donald Dansereau, Dimity Miller]
year: 2026
venue: "CVPR 2026"
tags: [change-detection, gaussian-splatting]
url: ""
pdf: "[[2026-online-scd-multiview-fusion.pdf]]"
date_ingested: 2026-06-18
---

# Changes in Real Time: Online Scene Change Detection with Multi-View Fusion

![[2026-online-scd-multiview-fusion-thumbnail.png]]

## Research gap

This paper presents the first online scene change detection (SCD) method that is simultaneously pose-agnostic, label-free, multi-view consistent, and real-time (>10 FPS). The approach builds a 3D Gaussian Splatting (3DGS) representation of a reference scene, then processes incoming post-change frames online to detect changes on the fly — without requiring the full post-change capture in advance. It surpasses even the best offline approaches on the PASLCD benchmark.

## Contributions

- **Self-supervised fusion loss (LSSF)**: jointly learns multi-view consistent change masks from pixel-level (L1 + D-SSIM) and feature-level (SAM2-Tiny) cues without hard thresholds or heuristic fusion
- **Online, real-time operation**: achieves >10 FPS with SOTA accuracy, closing the gap between online and offline SCD for the first time
- **PnP-based pose estimation**: constant-time O(1) pose estimation using XFeat descriptors triangulated against reference scene, avoiding drift accumulation
- **Change-guided selective reconstruction**: updates only changed regions of the 3DGS scene representation, enabling scene updates in seconds rather than full reconstruction
- **State-of-the-art results**: outperforms all prior online and offline methods on PASLCD, with significant margins in both mIoU and F1

## Method

The pipeline operates in five stages:

1. **Reference scene construction**: Standard 3DGS (Speedy-Splat) built from pre-change images with SfM-estimated poses
2. **Pose estimation**: For each incoming post-change frame, XFeat keypoints are matched to reference frames, 2D-3D correspondences established via triangulated reference points, and pose estimated via PnP+RANSAC with GPU-parallel miniBA refinement
3. **Change cue extraction**: Pixel-level cues (L1 + D-SSIM between rendered reference and observed post-change views) combined with feature-level cues (SAM2-Tiny feature map differences) via simple addition
4. **Change mask inference**: A change representation (Rchange) initialized from Rref with learnable change parameters is optimized online using LSSF. The loss encourages change parameters to activate where cues are strong while regularizing against trivial all-change solutions. Random sampling of past frames with bias toward recent observations enforces multi-view consistency
5. **Scene update**: After all observations, refined change masks guide selective reconstruction of changed regions only, followed by fusion with existing primitives and lightweight global optimization

## Datasets & evaluation

Evaluated on PASLCD (10 room-scale indoor/outdoor scenes) and CL-Splats (5 tabletop scenes). Achieves highest F1 and mIoU across all settings:
- **Online setting**: substantially outperforms all prior online methods (ChangeSim, SplatPose, OmniPoseAD) while maintaining >10 FPS
- **Offline setting**: surpasses MV3DCD and GeSCD despite operating in the harder online regime
- Scene representation updates complete in seconds vs. minutes for full reconstruction
- Ablations confirm both pixel and feature cues are necessary; SSF loss outperforms hard thresholding and intersection fusion

## Limitations

- XFeat features may fail under extreme appearance changes or textureless regions (acknowledged in supplementary)
- Assumes static scene within each revisit — moving objects during observation are not handled
- Requires a high-fidelity reference 3DGS, which needs sufficient viewpoint coverage of the pre-change scene
- Evaluation limited to room-scale indoor/outdoor scenes; scalability to building-scale or outdoor environments not demonstrated
- The 3DGS representation update, while fast, is a post-hoc step — truly continuous representation maintenance during observation remains open

## Key takeaways

The core innovation is a self-supervised fusion (SSF) loss that jointly integrates pixel-level and feature-level change cues into a persistent 3D change representation, replacing the hard-thresholded intersection heuristic used by prior work (MV3DCD). This enables the system to accumulate change evidence across viewpoints, suppressing view-dependent distractors (shadows, reflections, illumination shifts) while maintaining sensitivity to subtle changes. Additional contributions include a PnP-based pose estimator with O(1) complexity per frame using XFeat features, and a change-guided selective reconstruction strategy that updates only changed regions of the 3DGS representation in seconds.

Directly extends and surpasses **MV3DCD** (the previous SOTA offline multi-view SCD method) by replacing its hard-thresholded intersection heuristic with the learned SSF loss. Addresses the online/offline gap that **ChangeSim** identified but couldn't close due to its reliance on RGB-D SLAM and trajectory alignment assumptions. Complements **SceneDiff**'s training-free geometric alignment approach — while SceneDiff focuses on instance-level detection with foundation model composition, this work focuses on dense pixel-level change segmentation with online operation and scene representation updates. Builds on **GeSCF**'s zero-shot philosophy but achieves it through 3DGS rendering rather than foundation model adaptation.

The PnP-based pose estimation improves on SplatPose/OmniPoseAD's direct optimization against the representation, which suffers from convergence failures in complex scenes. The change-guided update strategy advances over CL-Splats and GaussianUpdate's continual learning approaches by being faster and more targeted.
