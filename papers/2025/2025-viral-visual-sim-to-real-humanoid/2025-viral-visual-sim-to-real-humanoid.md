---
title: "VIRAL: Visual Sim-to-Real at Scale for Humanoid Loco-Manipulation"
authors: [Tairan He, Zi Wang, Haoru Xue, Qingwei Ben, Zhengyi Luo, Wenli Xiao, Ye Yuan, Xingye Da, Fernando Castañeda, Shankar Sastry, Changliu Liu, Guanya Shi, Linxi Fan, Yuke Zhu]
year: 2025
venue: "CVPR 2026"
tags: [embodied-ai, robotics]
url: "https://arxiv.org/abs/2511.15200"
date_ingested: 2026-08-15
---

# VIRAL: Visual Sim-to-Real at Scale for Humanoid Loco-Manipulation

![[2025-viral-visual-sim-to-real-humanoid-thumbnail.png]]

## Research gap

Humanoid robots lack autonomous loco-manipulation — the tight coordination of locomotion and manipulation under onboard perception over long horizons. Existing systems either focus on blind locomotion, static tabletop manipulation, or rely on human teleoperation and non-onboard sensors. The "robotic foundation model" approach of collecting large-scale real-world teleoperation data faces prohibitive costs for humanoid mobile manipulation due to hardware complexity, high DoF, and safety constraints. Meanwhile, visual sim-to-real transfer — well-established for legged locomotion — has not been demonstrated for humanoid loco-manipulation with onboard RGB perception.

## Contributions

- **VIRAL framework**: a teacher-student pipeline that learns humanoid loco-manipulation entirely in simulation and deploys zero-shot to real hardware using only onboard RGB perception.
- **Technical recipe for RGB-based humanoid loco-manipulation**: identifies the critical design choices — delta action space, reference state initialization from demonstrations, DAgger-BC mixture distillation, large-scale visual domain randomization, and dexterous hand system identification — that make the full stack work.
- **Compute scaling analysis**: demonstrates that scaling GPU compute (up to 64 GPUs for student, 16 for teacher) is not merely beneficial but often necessary — low-compute regimes frequently fail to learn long-horizon loco-manipulation.
- **Real-world deployment**: achieves 54/59 consecutive successful loco-manipulation cycles on a Unitree G1, approaching expert-level teleoperation performance (100% success) while outperforming non-expert teleoperators (73%) and executing faster than the expert (20.2s vs. 21.4s per cycle).

## Method

**Teacher-student privileged learning**: A privileged RL teacher with full state access (proprioception + object/table transforms) learns the task, then a vision-based student observing only RGB images and on-robot proprioception is distilled from the teacher.

**Teacher policy**: Operates on top of a pretrained whole-body control (WBC) policy (HOMIE), outputting high-level delta commands (velocity, yaw, arm joints, finger joints) rather than raw motor torques. Key elements:
- **Delta action space**: increments accumulated into WBC commands, significantly accelerating and stabilizing RL training compared to absolute joint targets.
- **Reference state initialization (RSI)**: 200 teleoperated simulation demonstrations used as a state-initialization buffer for RL resets, exposing the policy to diverse rewarding states (grasping poses, placement configurations) before it can reach them from scratch. Without RSI, the teacher plateaus below 10% success.
- **Stage-based rewards**: walking toward objects, placing (penalizing grip force near tray), grasping (lifting height + goal position), and turning (yaw alignment).

**Student policy**: Distilled via a mixture of online DAgger (α=0.5) and behavior cloning. DINOv3 vision backbone extracts RGB features fused with proprioception. LSTM history architecture improves over single-step baselines.

**Sim-to-real transfer**: Three critical elements:
1. **Dexterous hand SysID**: system identification of finger armature, stiffness, and damping parameters for the high-gear-ratio Unitree 3-finger hand.
2. **Camera FOV alignment and randomization**: matching intrinsics to specs, lightweight real-to-sim extrinsics calibration, plus extrinsics randomization during training.
3. **Large-scale visual domain randomization**: material, dome-light, camera-extrinsics, image quality (brightness, contrast, hue, noise, blur), camera latency, object colors. Removing all randomization causes 35.1% performance drop.

Training uses Isaac Lab with tiled rendering on up to 64 L40S GPUs across 8 nodes.

## Datasets & evaluation

**Robustness**: 54/59 consecutive successful loco-manipulation cycles (walk → place → grasp → turn) on real Unitree G1 with onboard Intel RealSense D435i. Near expert-level teleoperation (100%) and substantially better than non-expert (73%).

**Generalization**: zero-shot transfer to variations in tray position, robot start pose, table height and type, tablecloth color, lighting conditions, and novel object categories — all without fine-tuning.

**Scaling ablations** (simulation):
- Teacher: 1–2 GPUs plateau below target; 8–16 GPUs consistently reach >90% success.
- Student: 1→64 GPUs shows clear scaling in convergence speed, training stability, and final success rate.
- Removing any single randomization component degrades performance; removing all causes 35.1% drop.

**Component ablations**: delta action space and RSI are both essential (without either, teacher fails). DAgger-BC mixture (α=0.5) outperforms pure BC (brittle) or pure DAgger. DINOv3 backbone outperforms alternatives. History-aware architectures outperform single-step.

## Limitations

- The task demonstrated (walk-place-grasp-turn between two tables) is a single loco-manipulation skill; extending to diverse tasks or longer-horizon chaining is not shown.
- Relies on a pretrained WBC policy (HOMIE) as an API layer, limiting the action space to what WBC supports.
- Real-world evaluation is on a single Unitree G1 robot in controlled indoor environments; robustness to outdoor conditions or different humanoid platforms is not tested.
- Requires substantial compute (64 GPUs for student training) which limits accessibility.
- No formal safety guarantees for deployment.

## Key takeaways

- Visual sim-to-real for humanoid loco-manipulation is now practical: a policy trained entirely in simulation transfers zero-shot to real hardware and approaches expert-level teleoperation performance.
- Compute scale is a critical but underappreciated variable — low-compute regimes don't just train slower, they frequently fail entirely for long-horizon tasks.
- The delta action space over a pretrained WBC policy is essential — it constrains the action space to safe, reliable humanoid motions while enabling RL to discover complex loco-manipulation behaviors that would require prohibitive reward engineering from scratch.
- Reference state initialization from a small number of teleoperation demonstrations (200) dramatically bootstraps RL exploration, reducing reliance on brittle reward tuning.
- DAgger-BC mixture distillation (50/50) outperforms either alone — BC provides fast initialization but brittle policies; DAgger provides robustness to compounding errors.
- This paper complements SONIC (same lab): SONIC provides the scalable motion tracking foundation and universal token space, while VIRAL provides the technical recipe for RGB-based sim-to-real loco-manipulation on top of such a foundation.
