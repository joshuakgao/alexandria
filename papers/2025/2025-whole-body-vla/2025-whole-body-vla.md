---
title: "WholeBodyVLA: Towards Unified Latent VLA for Whole-Body Loco-Manipulation Control"
authors: [Haoran Jiang, Jin Chen, Qingwen Bu, Li Chen, Modi Shi, Yanjie Zhang, Delong Li, Chuanzhe Suo, Chuang Wang, Zhihui Peng, Hongyang Li]
year: 2025
venue: "ICLR 2026"
tags: [embodied-ai, robotics]
url: "https://arxiv.org/abs/2512.11047"
date_ingested: 2026-08-19
---

# WholeBodyVLA: Towards Unified Latent VLA for Whole-Body Loco-Manipulation Control

![[2025-whole-body-vla-thumbnail.png]]

## Research gap
Existing humanoid control approaches—whether modular pipelines or end-to-end systems—lack manipulation-aware locomotion, confining robots to limited workspaces and preventing large-space loco-manipulation. Two root causes are identified: (1) scarcity of humanoid teleoperation data covering joint locomotion and manipulation, and (2) imprecise and unstable RL locomotion controllers that use continuous velocity-tracking objectives ill-suited for the start–stop semantics of manipulation tasks.

## Contributions
- **WholeBodyVLA**: A unified VLA framework enabling bipedal humanoids to perform end-to-end large-space loco-manipulation autonomously, the first system to do so without external modules or MoCap input.
- **Unified latent learning**: Separate locomotion and manipulation Latent Action Models (LAMs) trained on action-free egocentric videos, providing pseudo-action supervision for VLA pre-training and alleviating teleoperation data scarcity.
- **Loco-manipulation-oriented (LMO) RL policy**: Replaces continuous velocity tracking with a discrete command interface (`{-1, 0, 1}` for forward, lateral, turning plus a height scalar), yielding more precise and stable locomotion execution through a two-stage training curriculum.
- **Low-cost data collection pipeline**: A single operator with a head-mounted camera captures manipulation-aware locomotion videos, avoiding expensive MoCap or teleoperation setups.

## Method
The system has three layers:

1. **Latent Action Models (LAMs)**: Two separate VQ-VAE models built on DINOv2 features—one for manipulation (trained on AgiBot World data) and one for locomotion (trained on self-collected egocentric videos). Each encodes frame-to-frame inverse dynamics into discrete latent tokens. Training them separately avoids conflicts between static-camera manipulation data and moving-camera locomotion data.

2. **VLA pre-training and fine-tuning**: A VLM backbone is pre-trained to predict both manipulation and locomotion latent action tokens from egocentric images and language instructions. A lightweight action decoder is then attached and fine-tuned on teleoperation trajectories to output upper-body joint positions and a discrete locomotion command.

3. **LMO RL policy**: Converts discrete locomotion commands into lower-body joint torques at 50 Hz. Uses a two-stage curriculum: Stage I acquires basic gaits with progressive upper-body disturbance; Stage II refines directional accuracy (terminal heading deviation minimization) and manipulation stability (structured perturbations from real manipulation data, stand-still penalties).

The VLA runs at ~10 Hz, producing dual-arm joint targets and locomotion commands executed by the LMO policy.

## Datasets & evaluation
- **Platform**: AgiBot X2 humanoid (7-DoF arms, 6-DoF legs, 1-DoF waist, Intel RealSense D435i).
- **Teleoperation data**: 50 VR+joystick demonstrations per task.
- **Pre-training data**: AgiBot World (manipulation) + self-collected egocentric locomotion videos.
- **Core tasks**: Bag packing (bimanual grasp, sidestep, squat-place), box loading (squat-grasp, turn, place onto cart), cart pushing (grasp handle, push 50+ kg forward).
- **Results**: WholeBodyVLA achieves 78.0% average success rate vs. 64.0% for modular baselines, 42.0% for GR00T, and 56.7% for OpenVLA-OFT (all using the same LMO controller). Unified latent learning improves success by 38.7% over no-LAM ablation; separate LAMs outperform a shared LAM by 12 percentage points.
- **Extended tasks**: Terrain traversal, long-horizon multi-step sequences, visual navigation, vacuum cleaning, and stain wiping—WholeBodyVLA outperforms velocity-based RL variants across all settings.
- **Data scaling**: 50%+ human video pre-training matches variants with <25% videos fine-tuned on 8× more teleoperation data, confirming that latent learning reduces reliance on costly teleoperation.

## Limitations
- Struggles with very long-horizon and highly dexterous tasks requiring extended planning.
- Lacks explicit spatial memory or mapping for navigation in large environments.
- No active perception strategies for cluttered or dynamic scenes.
- Evaluated on a single robot platform (AgiBot X2); cross-embodiment transfer is unexplored.

## Key takeaways
- Decoupling locomotion and manipulation LAMs is critical—a single shared LAM on mixed data underperforms because manipulation videos have static cameras while locomotion videos have moving cameras, creating conflicting learning signals.
- Action-free egocentric videos are a surprisingly effective and low-cost supervision source for humanoid loco-manipulation, substantially reducing the need for expensive teleoperation data.
- Replacing continuous velocity tracking with a discrete command interface for the RL locomotion controller dramatically improves precision and stability for manipulation-relevant movements (advancing, turning, squatting), addressing a failure mode that accounts for most task failures in prior systems.
- The framework demonstrates that unified end-to-end VLA control of both locomotion and manipulation is feasible and outperforms modular approaches that treat them as separate skills.
