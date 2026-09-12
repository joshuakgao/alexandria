---
title: "DexImit: Learning Bimanual Dexterous Manipulation from Monocular Human Videos"
authors: [Juncheng Mu, Sizhe Yang, Yiming Bao, Hojin Bae, Tianming Wei, Linning Xu, Boyi Li, Huazhe Xu, Jiangmiao Pang]
year: 2026
venue: "RSS 2026"
tags: [robotics]
url: "https://www.roboticsproceedings.org/rss22/p003.pdf"
date_ingested: 2026-09-10
---

# DexImit: Learning Bimanual Dexterous Manipulation from Monocular Human Videos

![[2026-deximit-thumbnail.png]]

## Research gap

Data scarcity fundamentally limits bimanual dexterous manipulation — teleoperation with multi-fingered hands is expensive and difficult, making large-scale data collection impractical. Human manipulation videos are abundant and encode both high-level task concepts and low-level actions, but the embodiment gap between human hands and robotic dexterous hands makes direct pretraining ineffective. Existing video-to-robot methods either require absolute depth information, are highly sensitive to reconstruction noise (causing RL training failures), or cannot handle long-horizon bimanual tasks with complex physical interactions like tool use.

## Contributions

- Proposes **DexImit**, an automated four-stage pipeline that converts monocular human manipulation videos into physically plausible bimanual dexterous manipulation data without any additional information (no depth, camera parameters, or robot demonstrations).
- Introduces depth-free 4D reconstruction with near-metric scale, using human hand size as a metric prior and an align-render-align procedure for scale estimation.
- Designs an **Action-Centric Scheduling Algorithm** for subtask decomposition and bimanual coordination, enabling arbitrary-horizon tasks with mixed unimanual, cooperative, and concurrent bimanual actions.
- Develops force-closure-based grasp synthesis with MANO-prompted candidate ranking, producing stable grasps consistent with demonstrated human hand poses.
- Demonstrates comprehensive data augmentation (object pose/scale, camera pose, point cloud noise) that enables zero-shot sim-to-real transfer without any real-world data.
- Handles challenging tasks including tool use (cutting an apple), long-horizon manipulation (making a beverage), and fine-grained tasks (stacking six cups into a pyramid).

## Method

**Stage 1 — 4D Reconstruction**: Given a monocular video, DexImit uses Qwen3-VL for video understanding and object identification, Grounded SAM2 for segmentation, SpatialTracker v2 for depth estimation, SAM3D for image-to-3D object mesh generation, Wilor for hand pose estimation, and FoundationPose++ for 6D object pose tracking. Metric scale is recovered using human hand size as a prior via an align-render-align procedure. All trajectories are transformed into a unified world coordinate system using the table plane normal and hand positions.

**Stage 2 — Subtask Decomposition & Scheduling**: Tasks are decomposed into structured representations (task → subactions: pregrasp, grasp, motion, release) using Qwen3-VL. The Action-Centric Scheduling Algorithm dynamically assigns subtasks to embodiments via a priority queue, supporting arbitrary combinations of unimanual, cooperative bimanual, and concurrent bimanual actions over long horizons.

**Stage 3 — Action Generation**: Force-closure-based grasp synthesis generates physically feasible grasp candidates on object convex hulls, ranked by proximity to reconstructed human hand poses. Keyframe-based motion planning computes relative object transformations between frames and applies them to end-effector poses, treating hand-object as a rigid body after grasping.

**Stage 4 — Data Augmentation**: Object pose/scale randomization (0.8–1.2x), camera pose randomization, and point cloud noise augmentation (30% point removal + normal perturbation). Crucially, grasps are reused across scale augmentations rather than regenerated, as regeneration introduces inconsistent supervision that destabilizes imitation learning.

Policies are trained using 3D Diffusion Policy (DP3) on augmented datasets of ~100 demonstrations per task.

## Datasets & evaluation

**Reconstruction evaluation**: SpatialTracker v2 + FoundationPose++ achieves 82% success rate on 100 short-horizon tasks, substantially outperforming alternatives (VGGT+PCR: 32%, Trace-Anything+RANSAC: 38%).

**Data quality comparison** (simulation, success rate across 6 tasks):
- Put Cup: DexImit 100% vs. RigVid 96%, DexMan 94%
- Grapefruit: DexImit 100% vs. DexMan 98%
- Fruits: DexImit 100% vs. DexMan 100%
- Pour: DexImit 100% vs. DexMan 50%
- Pot: DexImit 78% (baselines fail)
- Stack 6 Cups: DexImit 52% (baselines fail)

**Usability analysis**: Data usability varies by input quality and task difficulty. Video-generated inputs (Veo3) achieve high usability for simple tasks; custom-captured videos maintain considerable success on long-horizon tasks; manual correction enables the most challenging fine-grained tasks.

**Zero-shot real-world deployment**: Evaluated on 4 meta-tasks with dual UR5e arms + XHands. Scale augmentation is critical — removing it significantly degrades performance. Regenerating grasps per scale is worse than no scale augmentation due to inconsistent supervision.

## Limitations

- Sequential multi-module pipeline means errors propagate — reconstruction failures account for a significant portion of failure cases.
- Cannot handle complex in-hand manipulation (e.g., finger gaiting, dexterous reorientation).
- Long-horizon tasks with generated or in-the-wild videos still have limited usability; custom-captured or manually corrected videos are needed for the hardest tasks.
- Restricted to rigid objects — deformable and articulated objects are not supported.
- Real-world evaluation limited to four meta-tasks; broader task diversity not validated on hardware.

## Key takeaways

- Human hand size provides a surprisingly effective metric scale prior for monocular reconstruction, enabling depth-free 4D trajectory recovery at near-metric scale from arbitrary viewpoints.
- The key insight for scaling video-to-robot pipelines is robustness to reconstruction noise: force-closure grasp synthesis + keyframe-based motion planning effectively mitigates compounding errors that cause RL-based approaches (like DexMan) to fail on multi-step tasks.
- Scale augmentation with consistent grasps is critical for sim-to-real transfer — the counterintuitive finding that regenerating grasps per scale *hurts* performance reveals that supervision consistency matters more than per-instance optimality in imitation learning.
- Video generation models (Veo3) are already usable as a data source for simple dexterous manipulation tasks, suggesting a path toward unlimited synthetic training data as video generation improves.
- The framework represents a significant step from the DAPG paradigm (Rajeswaran et al., 2018) which required VR teleoperation for 25 demonstrations — DexImit instead harvests demonstrations from arbitrary monocular video, dramatically lowering the data collection barrier for dexterous manipulation.
