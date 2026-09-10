---
title: "SONIC: Supersizing Motion Tracking for Natural Humanoid Whole-Body Control"
authors: [Zhengyi Luo, Ye Yuan, Tingwu Wang, Chenran Li, Fernando Castañeda, Sirui Chen, Zi-Ang Cao, Jiefeng Li, David Minor, Qingwei Ben, Jinhyung Park, David Sami, Zi Wang, Xingye Da, Runyu Ding, Cyrus Hogg, Lina Song, Edy Lim, Eugene Jeong, Tairan He, Haoru Xue, Wenli Xiao, Simon Yuen, Jan Kautz, Yan Chang, Umar Iqbal, Linxi Fan, Yuke Zhu]
year: 2026
venue: "Science Robotics"
tags: [embodied-ai, robotics]
url: "https://arxiv.org/abs/2511.07820"
date_ingested: 2026-08-14
---

# SONIC: Supersizing Motion Tracking for Natural Humanoid Whole-Body Control

![[2026-sonic-supersizing-motion-tracking-humanoid-thumbnail.png]]

## Research gap

Current humanoid controllers are small neural networks trained on a few GPUs for single tasks, requiring manual reward engineering per behavior. This fundamentally limits scalability — each new capability (walking, dancing, getting up) demands redesigned rewards and objectives. Adversarial imitation methods (AMP, ASE) provide a unified objective but suffer from mode collapse as motion dataset diversity grows. No prior work has demonstrated that scaling model capacity, data, and compute yields a general-purpose humanoid controller, nor has anyone shown how to bridge a universal motion tracker to diverse downstream applications (VR teleoperation, VLA-driven manipulation, multi-modal control) through a single unified interface.

## Contributions

- **Motion tracking as a scalable foundational task**: demonstrates that physics-based motion tracking exhibits favorable scaling properties — performance improves steadily with compute, data, and model size — enabling a single 42M-parameter policy trained on 100M+ frames (700 hours of mocap) over 21k GPU hours.
- **Universal token space**: a shared quantized representation with specialized encoders for robot, human, and hybrid motion inputs that unifies heterogeneous interfaces (VR teleoperation, video, text, music) within a single control policy.
- **Real-time kinematic motion planner**: an autoregressive planner that converts high-level intent (velocity, direction, style) into short-horizon reference motions at <5ms inference, enabling interactive control without retraining.
- **VLA-driven whole-body loco-manipulation**: demonstrates that the universal token space enables GR00T N1.5 VLA models to perform autonomous tasks requiring coordinated hand grasping and foot placement (e.g., stepping on a trash can pedal while throwing a can), achieving 75% average success across five tasks.

## Method

SONIC positions motion tracking — imitating reference human motion frame-by-frame on a simulated humanoid — as the foundational training task. The dense per-frame supervisory signal avoids the mode collapse of discriminator-based methods and the non-transferability of per-task reward engineering.

**Scaling axes**: The policy is scaled along three dimensions: network size (1.2M → 16M → 42M parameters), dataset volume (4M → 100M+ frames from 317K motion clips across diverse behaviors), and compute (2K → 9K → 21K GPU hours on up to 128 GPUs). Training uses Isaac Lab with the Unitree G1 humanoid.

**Universal token space**: Heterogeneous motion sources are encoded into a shared discrete representation via specialized encoders: a robot motion encoder for proprioceptive state, a human motion encoder for SMPL poses (from VR or video), and a hybrid encoder. VQ-VAE quantizes all inputs into a unified 64-dimensional token that the tracking policy consumes. This allows the same policy to handle VR teleoperation, video-based teleoperation, text/music-driven motion generation, and VLA outputs.

**Kinematic motion planner**: An autoregressive model generates short-horizon reference motions (0.8–2.4s segments) conditioned on user commands (velocity, direction, locomotion style). Inference runs at <5ms on laptop, ~12ms on Jetson Orin, with replanning every 100ms. A critically damped spring model filters unrealistic commands for safe deployment.

**VLA integration**: GR00T N1.5 VLA predicts 78-dimensional actions (64D universal motion token + 14D hand joints). The universal token space enables the VLA to control the full kinematic chain including feet, enabling loco-manipulation tasks that decouple upper-body/lower-body control approaches cannot achieve.

## Datasets & evaluation

**Motion tracking scaling**: On held-out test-content (7,016 clips of unseen motion categories), the largest model achieves 99.6% success with 23.8mm MPJPE-L, vs. 98.0%/27.7mm for the smallest. Gains are most pronounced on out-of-distribution motions. On PHUMA (68K clips from a different retargeting pipeline), achieves 97.2% success.

**Baseline comparisons**: SONIC achieves 98.5%/99.2%/97.2% success on test-content/test-repetition/PHUMA vs. 82.0%/85.4%/73.8% for BeyondMimic and 61.6%/69.4%/78.5% for Any2Track. MPJPE-L of 23.7mm is 42% lower than BeyondMimic (40.9mm).

**Specialist comparison**: Against OpenHomie (a locomotion-specific controller), SONIC achieves 98.5% survival vs. 43.0% across 0–5 m/s velocity tracking, demonstrating that a universal tracker outperforms specialized controllers even on their target task.

**Sim-to-real**: On 124 diverse motions deployed on real Unitree G1, achieves 99.2% success (vs. 100% in sim) with 25.7mm MPJPE-L (vs. 22.3mm in sim). Largest gap is in feet (53.7mm real vs. 29.0mm sim).

**VLA loco-manipulation**: 75% average success across five tasks (10–20 trials each), including a soda-can-to-trash-can task requiring sequential pick-up, navigation, foot pedal stepping, and throwing — 60% success.

## Limitations

- No formal treatment of safety or energy efficiency for extended real-world deployments.
- Under extreme conditions or very dynamic motions, the tracker may lose balance despite domain randomization and spring-model filtering.
- Sim-to-real gap is largest for foot placement (53.7mm vs. 29.0mm), reflecting difficulty of precise contact dynamics transfer.
- VLA integration requires teleoperation data collection for each new task; the data efficiency of this pipeline is not systematically studied.

## Key takeaways

- Motion tracking scales favorably because each frame provides an explicit target pose — the supervisory signal remains informative as dataset diversity grows, unlike discriminator-based methods where feedback degrades with scale.
- A universal tracker trained on diverse whole-body data outperforms specialist controllers even on their target tasks (98.5% vs. 43% survival on velocity tracking vs. OpenHomie), suggesting data diversity trumps task-specific optimization.
- The universal token space is a key architectural contribution: by quantizing heterogeneous motion sources into a shared discrete representation, a single policy handles VR teleoperation, video/text/music control, and VLA-driven autonomy without retraining.
- VLA-driven loco-manipulation with coordinated hand and foot placement (stepping on pedals, throwing objects) is now achievable — this was previously impossible with decoupled upper/lower body control approaches.
- The three-axis scaling analysis (data, model, compute) provides the first evidence that humanoid control follows scaling trends analogous to those observed in language and vision foundation models.
