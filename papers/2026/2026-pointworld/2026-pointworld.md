---
title: "PointWorld: Scaling 3D World Models for In-The-Wild Robotic Manipulation"
authors: [Wenlong Huang, Yu-Wei Chao, Arsalan Mousavian, Ming-Yu Liu, Dieter Fox, Kaichun Mo, Li Fei-Fei]
year: 2026
venue: "CVPR 2026"
tags: [world-models, 3d-scene-understanding, point-tracking]
url: "https://arxiv.org/abs/2601.03782"
pdf: "[[2026-pointworld.pdf]]"
date_ingested: 2026-06-19
---

# PointWorld: Scaling 3D World Models for In-The-Wild Robotic Manipulation

![[2026-pointworld-thumbnail.png]]

## Research gap
Existing world models for robotic manipulation either lack action conditioning (video generation models), require scene-specific engineering (physics simulators), or depend on domain-specific priors like objectness or material properties (learning-based dynamics models). No single pre-trained model generalizes across diverse in-the-wild environments, embodiments, and manipulation tasks from only a single RGB-D image without additional demonstrations or fine-tuning.

## Contributions
- Introduces **PointWorld**, a large pre-trained 3D world model that unifies state and action in a shared 3D point flow representation — given RGB-D input and robot actions (as 3D point flows from the robot's URDF), it predicts full-scene per-pixel 3D displacements.
- Curates and open-sources a large-scale 3D dynamics dataset (~2M trajectories, ~500 hours) spanning single-arm Franka, bimanual humanoid, and whole-body interactions across real (DROID) and simulated (BEHAVIOR-1K) domains.
- Develops a 3D annotation pipeline combining FoundationStereo (depth), VGGT (camera pose), and CoTracker3 (point tracking) to extract high-quality 3D point flows from real-world manipulation recordings.
- Presents rigorous scaling studies across backbones, objectives, features, data, and model size, distilling design principles for large-scale 3D world modeling.
- Demonstrates that a single pre-trained checkpoint enables zero-shot MPC-based manipulation (rigid pushing, deformable/articulated objects, tool use) on a real Franka robot from a single in-the-wild RGB-D capture.

## Method
**State-action representation**: Both scene state and robot actions are represented as 3D point flows. Scene points are back-projected from RGB-D; robot action points are generated via forward kinematics from the URDF, making the representation embodiment-agnostic. A point cloud backbone (PTv3) processes the concatenated scene + robot point cloud in a single forward pass, predicting per-point scene displacements over a 10-step horizon (1 second at 0.1s/step).

**Features**: Scene points are featurized with frozen DINOv3 features (multi-layer); robot points use temporal embeddings.

**Training objective**: Weighted Huber loss with (1) movement weighting to focus on points undergoing displacement, and (2) aleatoric uncertainty regularization to handle noisy real-world annotations. Occluded points (from CoTracker3 visibility labels) are excluded from supervision.

**Action inference**: Integrated with MPPI (sampling-based MPC) — samples K action perturbations, rolls out PointWorld for each, accumulates cost, and iteratively refines the trajectory. Real-time inference (0.1s per batched forward pass) enables efficient MPC.

**3D annotation pipeline**: For real-world data (DROID), replaces sensor depth with FoundationStereo, refines VGGT-initialized camera poses via optimization against known robot geometry, then lifts CoTracker3 2D tracks to 3D. Recovers reliable flows for >60% of DROID (~200 hours).

## Datasets & evaluation
- **Training data**: ~2M trajectories / ~500 hours from DROID (real, single-arm) and BEHAVIOR-1K (simulated, bimanual/whole-body). Simulation data filtered for active contact trajectories.
- **Evaluation metric**: Per-point per-timestep L2 distance on moving points (L2 mover) over 10-step prediction horizon. Expert-filtered test set retains top 80% most confident points.
- **Scaling results**: Log-linear improvement with both data scaling (5%→100%) and model scaling (50M→1B parameters). PTv3 backbone substantially outperforms GBND and PointNet baselines.
- **Key design findings**: (1) PTv3 is effective, efficient, and scalable for 3D dynamics; (2) movement weighting + uncertainty regularization + Huber loss stabilize training; (3) frozen DINOv3 features provide critical objectness priors; (4) model scaling from 50M to 1B yields smooth gains.
- **Real-world manipulation**: Zero-shot MPC on a Franka robot achieves 70% (tissue box pushing), 80% (scarf deformable), 90% (drawer articulated), 60% (broom tool use) among other tasks — all from a single RGB-D capture without demonstrations or fine-tuning.

## Limitations
- Requires calibrated RGB-D input (known camera intrinsics and depth), limiting deployment on RGB-only platforms.
- Short prediction horizon (1 second / 10 steps) — long-horizon planning requires receding-horizon MPC with re-observation.
- Point flow representation emphasizes geometry over appearance — tasks requiring visual/material reasoning (e.g., transparency, reflections) may not be well served.
- Real-world evaluation is limited to a single Franka arm; bimanual and whole-body transfer to real hardware is not demonstrated.
- MPPI-based action inference requires many rollouts per step, constraining the complexity of feasible planning problems.

## Key takeaways
- Unifying state and action as 3D point flows is a powerful inductive bias for world modeling — it naturally encapsulates objectness, articulation, material properties, and contact dynamics without explicit priors, while enabling cross-embodiment learning.
- The 3D annotation pipeline (FoundationStereo + optimized extrinsics + CoTracker3) unlocks large-scale real-world 3D dynamics data from existing manipulation datasets, turning DROID into a 3D training resource.
- Scaling laws apply to 3D world models: log-linear gains in prediction accuracy with both data and model scale, paralleling trends in language and 2D vision.
- A single pre-trained world model can serve as a general-purpose dynamics engine for zero-shot manipulation across diverse object types and tasks, suggesting that "next-token prediction" for 3D interaction may be a viable path to general-purpose robotic manipulation.
