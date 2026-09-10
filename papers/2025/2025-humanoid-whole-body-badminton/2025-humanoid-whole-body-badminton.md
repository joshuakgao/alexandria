---
title: "Humanoid Whole-Body Badminton via Multi-Stage Reinforcement Learning"
authors: [Chenhao Liu, Leyun Jiang, Yibo Wang, Kairan Yao, Jinchen Fu, Xiaoyu Ren]
year: 2025
tags: [robotics, embodied-ai]
url: "https://arxiv.org/abs/2511.11218"
date_ingested: 2026-08-21
---

# Humanoid Whole-Body Badminton via Multi-Stage Reinforcement Learning

![[2025-humanoid-whole-body-badminton-thumbnail.png]]

## Research gap
While humanoid robots have demonstrated locomotion and manipulation in static environments, dynamic real-world interactions with fast-moving objects under tight reaction windows remain largely unsolved. Badminton intensifies challenges found in robotic table tennis: highly uncertain aerodynamics, large-amplitude swings that destabilize balance, and the need for footwork and striking to co-evolve rather than being controlled independently. Prior humanoid racket-sport systems either rely on motion capture demonstrations, decouple upper and lower body control, or reduce striking to a 2D problem via virtual hit planes.

## Contributions
- First real-world demonstration of autonomous humanoid badminton, with a unified whole-body controller that coordinates footwork and striking on a 21-DoF humanoid without motion priors or expert demonstrations.
- A three-stage RL curriculum — footwork acquisition, precision-guided swing generation, and task-focused refinement — that jointly optimizes legs and arms for the hitting objective.
- A prediction-free variant that removes the EKF-based trajectory predictor, inferring timing and hitting targets implicitly from short-horizon shuttle observations, improving robustness to aerodynamic variability.
- Real-world validation with outgoing shuttle speeds up to 19.1 m/s, sub-second reaction windows (~0.7 s after prediction), and sustained 21-hit rallies in simulation.

## Method
**Robot platform**: A 1.28 m, 30 kg humanoid with 21 DoF (6 per leg, 1 waist, 4 per arm), IMU and joint encoders. A standard badminton racket is rigidly clamped to the forearm.

**Three-stage curriculum**:
1. **Stage 1 — Footwork acquisition**: Locomotion rewards (base height, orientation, symmetric contact forces, gait timing) plus a target approach reward that drives the robot toward the interception point. No hitting rewards yet.
2. **Stage 2 — Swing generation**: Introduces hitting rewards (swing speed, racket-y alignment, hold position) alongside locomotion rewards. A pre-generated corpus of ~20K shuttlecock flight trajectories provides training targets sampled from a 1.5–1.6 m height band.
3. **Stage 3 — Task-focused refinement**: Reduces locomotion regularizers (removes feet height, feet distance, step symmetry penalties) to allow the policy to discover more aggressive, performance-maximizing motions.

**Policy architecture**: PPO with privileged critic. Actor receives hitting target (position, orientation, time), proprioception, and dual-history features. Critic additionally observes privileged information. Actor/critic MLPs: (512, 256, 128) with ELU. Policy runs at 50 Hz; low-level PD controller at 500 Hz.

**Deployment**: An Extended Kalman Filter estimates shuttlecock trajectory from motion capture, predicting the interception target tuple (hit time, position, racket orientation). The first ~0.36 s is consumed by prediction, leaving <0.7 s for the controller to react.

**Prediction-free variant**: Replaces the EKF target with instantaneous shuttle position plus 5-frame history. The actor infers timing and target implicitly; the critic retains privileged target access. This simplifies deployment and naturally handles aerodynamic randomization.

**Sim-to-real**: Trained fully in simulation with domain randomization (friction, push perturbations, added mass/inertia, sensor noise). Zero-shot transfer without system identification.

## Datasets & evaluation
**Simulation**: Two robots sustain rallies of 21 consecutive hits. Ablation studies validate each curriculum stage and the prediction-free variant.

**Real-world experiments** (MoCap arena):
- **Machine-served shuttles**: Outgoing shuttle speeds up to 19.1 m/s, mean return landing distance of 4 m.
- **Human-robot rallies**: Fully autonomous rallies with human players demonstrated.
- **Virtual-target swinging repeatability**: Over 20 trials, the EKF-based policy achieves mean positional error of 23.21 mm (std 10.55 mm); the prediction-free variant achieves 54.00 mm (std 18.76 mm).
- **Prediction-free comparison**: Comparable hitting performance to EKF-based policy despite no explicit trajectory prediction.

**Pre-strike leg torque analysis**: The controller produces significant torque peaks on the supporting leg ~0.1 s before striking, demonstrating that the legs actively contribute to power generation through coordinated push-off, not just repositioning.

## Limitations
- Relies on external motion capture for base state and shuttle tracking — not deployable in unstructured environments.
- The racket is rigidly mounted (no wrist articulation), limiting stroke variety and control over shuttle placement.
- Real-world evaluation is in a controlled MoCap arena with machine-served shuttles or cooperative human rallies — adversarial play and varied court conditions are not tested.
- The prediction-free variant has roughly 2x the positional error of the EKF-based policy on swing repeatability.
- No onboard vision — perception is fully offloaded to external infrastructure.

## Key takeaways
- Whole-body coordination emerges naturally from a unified RL policy with stage-wise curriculum: the legs learn push-off behaviors that actively contribute to striking power, not just locomotion.
- The prediction-free variant demonstrates that implicit temporal reasoning from short observation histories can substitute for explicit trajectory prediction, suggesting a path toward more robust reactive control.
- Badminton's demands (sub-second reaction, large-amplitude swings, aerodynamic uncertainty) make it a significantly harder testbed than table tennis for whole-body humanoid control.
- Zero-shot sim-to-real transfer with moderate domain randomization is sufficient for this dynamic task, without requiring system identification or real-world fine-tuning.
