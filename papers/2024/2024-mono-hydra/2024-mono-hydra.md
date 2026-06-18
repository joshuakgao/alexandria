---
title: "Mono-Hydra: Real-time 3D Scene Graph Construction from Monocular Camera Input with IMU"
authors: [Priyanka Udugama, George Vosselman, Francesco Nex]
year: 2024
venue: "ISPRS"
tags: [embodied-memory, scene-graphs, slam, depth-estimation]
url: ""
pdf: "[[2024-mono-hydra.pdf]]"
date_ingested: 2026-06-18
---

# Mono-Hydra: Real-time 3D Scene Graph Construction from Monocular Camera Input with IMU

![[2024-mono-hydra-thumbnail.png]]

## Research gap

Mono-Hydra extends the Hydra framework for real-time 3D scene graph construction to work with a monocular camera + IMU setup, eliminating the RGB-D or LiDAR dependency. The system replaces Hydra's depth sensor input with deep learning-based monocular depth estimation (DistDepth or LiteMono) and substitutes the stereo VIO with a robocentric visual-inertial odometry system (R-VIO2). Combined with HRNet semantic segmentation, the predicted depth and semantics are fused into a 3D semantic mesh that feeds into Hydra's existing scene graph construction pipeline.

## Contributions

- First real-time 3D scene graph construction system from monocular camera + IMU, extending Hydra to lightweight sensor setups
- Integration of self-supervised monocular depth estimation (DistDepth, LiteMono) with the Hydra scene graph pipeline
- Robocentric VIO (R-VIO2) replacing stereo VIO for improved consistency with monocular input
- Real-world multi-floor scene graph construction at 15 FPS on a laptop GPU
- Public release of code and real-world datasets

## Method

**Depth Estimation**: Two self-supervised monocular depth networks evaluated — DistDepth (structure distillation from DPT for metric-accurate indoor depth) and LiteMono (lightweight encoder with CDC and LGFI modules). Both fine-tuned on NYUv2 for indoor scenes.

**Semantic Segmentation**: HRNet pretrained on ADE20k (150 classes), truncated to 20 classes to match Hydra's configuration. Selected for highest pixel accuracy (80.77%) despite lower FPS (5.8 FPS on Titan Xp).

**VIO**: R-VIO2 robocentric visual-inertial odometry with square-root information filter, replacing Kimera's stereo VIO. Avoids observability mismatch issue of world-centric formulations.

**3D Semantic Mesh**: depth_image_proc ROS package combines predicted depth + semantic segmentation into XYZRGB point cloud, which feeds into Hydra's scene graph construction (ESDF → GVD → places → rooms → buildings).

**Resource Division**: Deep learning networks on GPU (CUDA), Hydra on CPU (8 threads), R-VIO2 on remaining CPU.

## Datasets & evaluation

Synthetic (uHumans2): DistDepth achieves 0.05–0.28m ME vs. Hydra with GT depth; LiteMono achieves 0.03–0.23m ME but with higher variance (ceiling regions problematic). Real-world (ITC building): DistDepth achieves 0.19m ME / 0.18m SD on 2nd floor; LiteMono achieves 0.39m ME / 0.27m SD. Multi-floor scene graph construction demonstrated (2nd to 3rd floor including stairs). Sub-20cm error achievable with DistDepth, enabling practical scene graph construction from a monocular camera.

## Limitations

- Semantic segmentation bottleneck at ~10-15 FPS limits overall system throughput; newer models (SAM, Mask2Former) could improve speed and accuracy
- HRNet's ADE20k class set (truncated to 20) severely limits object vocabulary compared to open-vocabulary approaches (HOV-SG)
- Monocular depth accuracy degrades in low-texture regions (ceilings, walls) and near object boundaries — ceiling depth errors with LiteMono are notable
- R-VIO2 provides odometry but no loop closure; drift accumulates in long trajectories
- Real-world evaluation limited to a single building; generalization to diverse indoor environments is untested
- No comparison against newer depth estimation methods (Depth Anything, Metric3D) which would likely improve metric accuracy significantly

## Key takeaways

The system produces a five-layer hierarchical scene graph (buildings → rooms → places → objects → metric-semantic mesh) in real-time at 15 FPS on a laptop GPU (NVIDIA RTX 3080). The semantic segmentation network (HRNet at ~10-15 FPS) is the bottleneck. On the synthetic uHumans2 dataset, the 3D mesh achieves 0.03–0.28m mean error compared to Hydra with ground-truth depth. On real-world data (ITC building, multi-floor), the mesh achieves 0.19–0.39m mean error compared to LiDAR ground truth, with successful multi-floor scene graph construction including stairs.

Mono-Hydra is a direct engineering extension of Hydra (Hughes et al., 2022), replacing depth sensors with learned monocular depth prediction. The core scene graph construction algorithm (ESDF → GVD → places → rooms) is unchanged from Hydra. The contribution is showing that modern self-supervised depth estimation is good enough (sub-20cm on synthetic, ~20cm on real) to feed Hydra's pipeline without an RGB-D camera.

Within the embodied memory topic, Mono-Hydra addresses the practical deployment constraint that most scene graph systems (Hydra, HOV-SG, MoMa-LLM) assume RGB-D input. By enabling monocular construction, it opens these systems to platforms where depth sensors are impractical (e.g., UAVs, lightweight robots). However, the accuracy degradation (0.19m vs. near-zero for depth sensors) may limit applicability for tasks requiring precise spatial reasoning.

Compared to Memory Over Maps (which also operates on posed RGB images), Mono-Hydra invests in full 3D reconstruction rather than on-demand geometry, but both share the goal of working with minimal sensor requirements.
