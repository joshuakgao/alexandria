---
title: "VITRA: Scalable Vision-Language-Action Model Pretraining for Robotic Manipulation with Real-Life Human Activity Videos"
authors: [Qixiu Li, Yu Deng, Yaobo Liang, Lin Luo, Lei Zhou, Chengtang Yao, Lingqi Zeng, Zhiyuan Feng, Huizhi Liang, Sicheng Xu, Yizhong Zhang, Xi Chen, Hao Chen, Lily Sun, Dong Chen, Jiaolong Yang, Baining Guo]
year: 2025
venue: "ICRA 2026"
tags: [vision-language-action, embodied-ai]
url: "https://arxiv.org/abs/2510.21571"
date_ingested: 2026-07-29
---

# VITRA: Scalable Vision-Language-Action Model Pretraining for Robotic Manipulation with Real-Life Human Activity Videos

![[2025-vitra-thumbnail.png]]

## Research gap
Existing VLA models for dexterous manipulation rely on teleoperated robot data collected in lab settings, which is expensive and limited in skill, object, and scene diversity. Internet-scale human videos offer abundant manipulation examples but are unstructured — unscripted, unsegmented, lacking action labels and language instructions. No prior work had transformed large-scale, unstructured human videos with explicit 3D action labels into VLA-aligned training data for pretraining.

## Contributions
- A fully-automated holistic framework that converts arbitrary unstructured egocentric human hand videos into structured V-L-A episodes aligned with robotic data in task granularity and labels, requiring no human annotation.
- A hand V-L-A dataset of 1M episodes and 26M frames from Ego4D, Epic-Kitchen, EgoExo4D, and Something-Something-V2, with vastly greater visual and linguistic diversity than existing robot datasets.
- A dexterous hand VLA model architecture with a PaliGemma-2 VLM backbone and DiT-based diffusion action expert, featuring causal action denoising and trajectory-aware augmentation.
- Strong zero-shot hand action prediction in completely unseen environments, and significant improvement over baselines when fine-tuned on small-scale real robot data (71.0% seen, 64.6% unseen vs. next-best 46.9% and 16.1%).

## Method
The framework has three stages for converting human video to VLA data:

1. **3D Motion Labeling**: Recovers per-frame metric-scale 3D hand poses (6D wrist pose + MANO joint angles) and camera poses from monocular video using HaWoR for hand reconstruction and modified MegaSAM for visual SLAM. Camera intrinsics estimated via DroidCalib (moving cameras) or MoGe-2/DeepCalib (static cameras).

2. **Atomic Action Segmentation**: Segments continuous video into atomic-level action clips by detecting speed minima of 3D hand wrist trajectories in world space. This simple approach requires no additional model inference or pre-annotated labels, and produces segments aligned with typical robot data granularity.

3. **Instruction Labeling**: For each segment, 3D hand trajectories are projected onto sampled video frames and fed to GPT-4.1, which determines whether the clip contains meaningful manipulation and generates imperative language descriptions.

The VLA model uses PaliGemma-2 (3B, SigLIP vision encoder + Gemma-2 LM) as the VLM backbone, with a learnable cognition token whose output conditions a DiT-Base diffusion action expert. The action space represents both hands via wrist translation/rotation deltas and 15 MANO joint angles (102 dimensions total). Causal attention in the action expert prevents zero-padded positions from affecting earlier predictions. Trajectory-aware augmentation (random crop, perspective warp, flipping with corresponding action transforms) enhances visual diversity.

For robot fine-tuning, robot end-effector actions are mapped to the human hand action space via topological joint correspondence, with unmapped dimensions zero-padded.

## Datasets & evaluation
- **Zero-shot hand action prediction benchmark**: 47 unseen grasping environments (396 objects) and 117 unseen general action environments. VITRA achieves 8.8cm average hand-object distance (vs. 19.1cm for Being-H0, 17.6cm for EgoDex-trained model) and highest user study scores (1.91 vs. 0.15 for Being-H0).
- **Real-world robot manipulation**: 4 tasks (pick & place, functional grasping, pouring, sweeping) on Realman robot with 12-DoF XHand, fine-tuned on 1.2K trajectories. Seen tasks: 71.0% success (vs. 46.9% π0, 46.0% latent action, 41.3% OXE pretrain, 32.1% no pretrain). Unseen objects/backgrounds: 64.6% (vs. 16.1% π0, 10.9% no pretrain, 7.8% OXE). Unseen categories: 70.8% pick & place.
- **Data scaling**: Approximately linear improvement on log scale — performance steadily increases with more episodes, and even 10% of VITRA data outperforms the full EgoDex dataset due to higher diversity.

## Limitations
- 3D hand reconstruction from monocular video introduces noise and imprecision, particularly for fast motions and heavy occlusion.
- Speed-minima segmentation can over-segment repetitive actions (e.g., wiping back and forth), requiring post-hoc merging after instruction labeling.
- The human-to-robot joint mapping is a simple topological correspondence — more sophisticated retargeting strategies are left as future work.
- Pretraining currently uses existing egocentric datasets (Ego4D, Epic-Kitchen, etc.) rather than truly web-scale video, though the framework has no technical barriers to further scaling.

## Key takeaways
- Explicit 3D hand action labels recovered from monocular video outperform latent action representations (LAPA-style) for VLA pretraining — VITRA achieves 71.0% vs. 46.0% for latent action pretraining on seen robot tasks, and 64.6% vs. 0% on unseen tasks.
- Visual and linguistic diversity of pretraining data matters more than sheer scale — VITRA's in-the-wild data vastly outperforms lab-captured EgoDex (300K+ episodes) even at smaller subset sizes, because real-life videos cover a broader spectrum of objects, scenes, and actions.
- Atomic action segmentation via 3D wrist speed minima is a surprisingly effective, compute-free alternative to learned temporal segmentation, producing clips well-aligned with robot data granularity.
- The framework makes every egocentric camera wearer a potential robot teacher — requiring only a single uncalibrated webcam with no constraints on activities or environments.
