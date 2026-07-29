---
title: "Perception-and-Action System for Humanoid Robot Task Execution in Construction"
authors: [Yanxi Liu, Yizhi Liu]
year: 2026
venue: "Computer-Aided Civil and Infrastructure Engineering"
tags: [built-environment]
url: "https://doi.org/10.1016/j.cacaie.2026.100107"
date_ingested: 2026-07-23
---

# Perception-and-Action System for Humanoid Robot Task Execution in Construction

![[2026-perception-action-humanoid-construction-thumbnail.png]]

## Research gap
While humanoid robots have human-like morphology well-suited to construction sites (designed for human dimensions, tools, and workflows), no prior work has established a method for humanoid robots to learn and execute real-world construction tasks. Two specific technical gaps exist: (1) human pose estimation outputs cannot be directly used by humanoid robots due to morphological mismatch (different limb proportions, joint structures, range of motion), and (2) even with retargeted poses, converting kinematic references into physically executable whole-body actions while maintaining balance and contact stability remains unsolved for construction settings.

## Contributions
- **Humanoid-PoseNet**: A vision-based perception module that extracts 3D human poses from RGB video of worker demonstrations and retargets them into humanoid-compatible pose trajectories via a shared-latent-space encoder-decoder architecture with triplet contrastive loss, reconstruction loss, and latent-consistency loss.
- **Humanoid-ActionNet**: A teacher-student reinforcement learning framework for whole-body control that converts retargeted pose trajectories into physically executable actions. The teacher policy trains with privileged observations using PPO with physics-aware rewards (motion tracking, balance, contact consistency); the student policy distills this into a deployable controller using only on-board proprioception.
- First demonstrated system enabling a humanoid robot (Unitree G1) to learn and execute construction tasks from human demonstrations, validated in both simulation (IsaacGym, MuJoCo) and real-world deployment.

## Method
The Vision-based Perception-and-Action (VPA) system has two stages:

**Humanoid-PoseNet** uses an off-the-shelf 3D pose estimator (PoseNet, fine-tuned on construction worker data) to extract 21-joint 3D human poses from RGB video. A human-to-humanoid retargeting network then maps these to a 14-joint humanoid configuration via two MLP encoders (human and robot) sharing a latent space and one decoder. Training uses three losses: a triplet loss (L1) based on bone-direction angular error for cross-skeleton alignment, a reconstruction loss (L2) for humanoid-domain fidelity, and a latent-consistency loss (L3) for cross-domain distribution alignment.

**Humanoid-ActionNet** uses a teacher-student RL architecture. The teacher policy receives privileged observations (DoF/keypoint differences, root velocity), proprioceptive states, and motion tracking targets, and is trained with PPO using physics-aware rewards covering DoF tracking, keypoint tracking, root velocity, body rotation, upper-body coherence, foot air-time, and foot-slip penalties. The student policy replaces privileged observations with a 10-frame proprioceptive history window, distilled via DAgger. A PD controller converts policy outputs to joint torques for hardware deployment on the Unitree G1 (23 actuated DoFs).

## Datasets & evaluation
- **Training data**: Five subjects performed 30 construction-related actions (carrying, lifting, climbing, signaling, etc.) captured with OptiTrack motion capture (120 Hz) and synchronized RGB video (30 fps), yielding ~150 minutes of demonstrations and 9,000 paired RGB-3D samples.
- **Pose estimation**: PoseNet achieved 58.3 mm MPJPE on the test set, outperforming AlphaPose (60.6), VideoPose3D (60.3), and 3D-PoseNet (61.0).
- **Retargeting**: Average MPJPE of 48.46 mm across 14 humanoid joints. Ablation showed all three losses contribute (removing L1: +57.91 mm, L2: +37.33 mm, L3: +8.76 mm).
- **Whole-body control**: Eight construction actions evaluated in sim-to-sim (IsaacGym to MuJoCo) and sim-to-real transfer on a physical Unitree G1. Average MPJPE of 82.45 mm across all actions. Humanoid-ActionNet outperformed ExBody (102.75), OmniH2O (96.35), ExBody+AMP (90.19), and ExBody2 (87.48).
- Demonstrated adaptability to different humanoid platforms (Unitree H1 and G1).

## Limitations
- Small dataset: only five subjects performing 30 actions, limiting generalizability across diverse worker populations and body types.
- No modeling of tool or material interaction — the system learns posture-based motion imitation only, without grasping or force-based manipulation.
- Non-negligible failure cases in sim-to-sim transfer (loss of balance, unstable recovery, tracking breakdown), attributed to difficulty capturing contact-rich construction dynamics in reward functions and simulator mismatches.
- Experiments focus on single short-horizon actions; long-horizon multi-step construction tasks (e.g., building a wall) are not addressed.
- Hand-level mismatch between simulation and hardware (three-finger gripper on G1) limits evaluation of manipulation-heavy tasks.

## Key takeaways
- Learning from human demonstration via pose retargeting and RL-based whole-body control is a viable path for enabling humanoid robots to perform construction tasks, achieving reliable execution across eight tested actions on real hardware.
- The retargeting network's shared latent space with contrastive learning effectively bridges the morphological gap between human and humanoid skeletons, and generalizes across different humanoid platforms.
- Physics-aware reward design tailored to construction task characteristics (load-bearing, coordinated upper/lower body motion) provides meaningful improvement over general-purpose humanoid controllers designed for locomotion or expressive movement.
- Construction robotics may benefit from humanoid platforms that consolidate multiple specialized robots, but significant challenges remain in payload, dexterity, and long-horizon task planning before practical deployment.
