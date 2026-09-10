---
topic: Pose Estimation
slug: pose-estimation
---

# Pose Estimation

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "pose-estimation")
SORT year DESC
```

## Overview
Pose estimation encompasses methods for recovering 2D and 3D human body configurations from images and video. The field spans three major threads: (1) skeleton-based methods that estimate joint locations and model their spatial-temporal dynamics for downstream tasks like action recognition, (2) real-time multi-person 2D pose estimation optimized for deployment on diverse hardware, and (3) full-body human mesh recovery (HMR) that estimates dense 3D surface meshes including articulated hands and feet. ST-GCN (Yan et al., AAAI 2018) established the foundational approach for skeleton-based action recognition by formulating dynamic skeletons as spatial-temporal graphs and applying graph convolutional networks. RTMPose (Jiang et al., 2023) systematically addressed the gap between academic accuracy and industrial deployability, showing that top-down pipelines with real-time detectors and SimCC-based coordinate classification can achieve strong accuracy at hundreds of FPS across CPU, GPU, and mobile platforms. SAM 3D Body (Yang et al., CVPR 2026) represents the frontier of HMR, using promptable encoder-decoder architectures, new parametric body representations (MHR) that decouple skeletal structure from surface shape, and VLM-driven data engines for mining challenging in-the-wild training examples.

## Trends
- **Graph-based skeleton modeling**: ST-GCN introduced graph convolution on body joint graphs with spatial configuration partitioning (centripetal/centrifugal grouping), enabling automatic learning of body part relationships without manual assignment. This approach generalizes across different skeleton formats (2D estimated vs. 3D tracked) and capture conditions.
- **Coordinate classification over heatmaps**: SimCC-based methods (as adopted by RTMPose) treat keypoint localization as 1D classification along each axis, eliminating costly heatmap decoding and enabling simpler, more deployment-friendly architectures.
- **Engineering-driven Pareto improvements**: RTMPose demonstrates that systematic optimization of training strategy (augmentation, scheduling, label smoothing) combined with deployment-aware backbone selection (CSPNeXt) yields large practical gains without architectural novelty.
- Shift from body-only to unified full-body (body + hands + feet) mesh recovery in a single model.
- Promptable architectures enabling user-guided inference for ambiguous poses.
- VLM-driven data engines that iteratively mine challenging in-the-wild images, prioritizing data diversity over scale alone.
- New parametric body representations (MHR, ATLAS) that decouple skeletal structure from surface shape for improved interpretability and controllability.
- Multi-stage annotation pipelines combining sparse/dense keypoint detection, differentiable mesh fitting, and multi-view geometry for high-quality pseudo ground truth at scale.
- Skeleton-based features are complementary to appearance (RGB) and motion (optical flow) modalities — ensemble fusion consistently improves action recognition.

## Open questions
- Whether unified body-hand models can fully close the gap with hand-specialized methods, especially on in-domain hand benchmarks.
- How to eliminate dependence on external camera intrinsic estimators for robust in-the-wild deployment.
- Whether new body representations (MHR) will achieve broad adoption or remain niche compared to the SMPL ecosystem.
- Scaling promptable HMR to multi-person scenes with complex interactions and mutual occlusion.
- Extending robust mesh recovery to dynamic video settings while maintaining per-frame accuracy.
- Learning graph convolution partitioning strategies end-to-end rather than using hand-designed rules.
- Whether coordinate classification approaches like SimCC can scale to whole-body and dense keypoint settings without sacrificing the spatial precision of heatmap methods.
