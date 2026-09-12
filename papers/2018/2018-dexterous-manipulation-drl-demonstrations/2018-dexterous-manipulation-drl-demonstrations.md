---
title: "Learning Complex Dexterous Manipulation with Deep Reinforcement Learning and Demonstrations"
authors: [Aravind Rajeswaran, Vikash Kumar, Abhishek Gupta, Giulia Vezzani, John Schulman, Emanuel Todorov, Sergey Levine]
year: 2018
venue: "RSS 2018"
tags: [robotics]
url: "https://arxiv.org/abs/1709.10087"
date_ingested: 2026-09-10
---

# Learning Complex Dexterous Manipulation with Deep Reinforcement Learning and Demonstrations

![[2018-dexterous-manipulation-drl-demonstrations-thumbnail.png]]

## Research gap

Prior work on model-free reinforcement learning for robotic manipulation was limited to simpler tasks with low-DoF manipulators (2–3 fingers or whole-arm). Model-based trajectory optimization could handle dexterous hands in simulation but required accurate dynamics models and state estimates, making real-world transfer impractical. Furthermore, pure RL on high-dimensional dexterous manipulation required extensive manual reward shaping, produced unnatural motions, and was far too sample-inefficient for physical robot training.

## Contributions

- First demonstration that model-free DRL can solve complex manipulation tasks with a 24-DoF anthropomorphic hand (ADROIT) from scratch in simulation, across four task categories: object relocation, in-hand manipulation, tool use, and door opening.
- Proposes **DAPG (Demonstration Augmented Policy Gradient)**, which combines behavior cloning pre-training with an augmented policy gradient loss that incorporates demonstration data throughout RL fine-tuning.
- Shows that DAPG reduces sample complexity by up to 30x compared to RL from scratch with shaped rewards, bringing training time to a few robot-hours — potentially feasible for real-world learning.
- Demonstrates that demonstration-augmented policies are significantly more robust to environment variations (object mass/size) and produce more natural, human-like motions compared to pure RL policies.
- Introduces a benchmark suite of four dexterous manipulation tasks representative of real-world challenges.

## Method

**Task setup**: Four tasks using the 24-DoF ADROIT hand in MuJoCo — object relocation, pen repositioning (in-hand), door opening (with latch), and hammering a nail. Each task has randomized initial conditions and both sparse (task completion) and shaped reward variants.

**Demonstrations**: 25 human demonstrations per task collected via a VR system (CyberGlove III + HTC Vive), recording full state-action trajectories in simulation. Small actuator noise is added during collection to improve robustness.

**DAPG algorithm**:
1. **Behavior cloning pre-training**: Initialize the policy by maximizing the likelihood of demonstration actions via supervised learning. The cloned policy typically cannot solve tasks on its own due to distribution shift, but provides a good initialization for RL exploration.
2. **RL fine-tuning with augmented loss**: Uses Natural Policy Gradient (NPG) with an additional gradient term that steers the policy toward demonstration actions. The weighting is: `w(s,a) = λ₀ · λ₁ᵏ · max(Aᵖ)`, where `λ₀ = 0.1`, `λ₁ = 0.95`, and `k` is the iteration count. This asymptotically decays the demonstration influence as the policy improves, allowing RL to eventually surpass the demonstrator.

The augmented gradient is used within the NPG covariant update (Fisher information pre-conditioning), combining the stability of on-policy methods with the efficiency of demonstration bootstrapping.

## Datasets & evaluation

Evaluated on four tasks with the ADROIT hand platform in MuJoCo simulation:

- **Object relocation**: DAPG achieves >90% success in ~52 iterations (~5.8 robot-hours) vs. 880 iterations for shaped-reward RL. Pure sparse-reward RL never succeeds.
- **In-hand manipulation (pen)**: DAPG succeeds in ~30 iterations (~3.3 hours) vs. 864 iterations for shaped RL.
- **Door opening**: DAPG succeeds in ~42 iterations (~4.7 hours) vs. 146 iterations for shaped RL. Sparse RL never succeeds.
- **Tool use (hammer)**: DAPG succeeds in ~55 iterations (~6.1 hours) vs. 448 iterations for shaped RL. Sparse RL never succeeds.

DAPG significantly outperforms DDPGfD (the primary demonstration-augmented baseline) across all tasks, with DDPGfD showing little progress within the same time budget. Robustness analysis on object relocation shows DAPG policies generalize across a wide range of object masses and sizes, while shaped-reward RL policies fail outside a narrow training distribution.

## Limitations

- All experiments are in simulation only — no real-world hardware validation, so the sim-to-real gap remains unaddressed.
- Demonstrations are collected via a specialized VR setup (CyberGlove III + HTC Vive), which may not be available or practical for all tasks and labs.
- The in-hand manipulation task used a computational expert rather than human demonstrations due to lack of tactile feedback in VR, raising questions about how demonstration quality affects DAPG in the hardest contact-rich tasks.
- Only sparse and shaped rewards are compared; no curriculum learning or other exploration strategies are evaluated as alternatives.
- The 24-DoF hand is simulated with position control; real hardware introduces additional challenges from torque control, sensor noise, and compliance.

## Key takeaways

- A small number of human demonstrations (25 per task) can dramatically transform the RL landscape for dexterous manipulation — not just accelerating learning but fundamentally changing the quality and robustness of learned behaviors.
- The human prior encoded in demonstrations biases learning toward robust strategies that generalize across environment variations, while pure RL with shaped rewards converges to brittle, idiosyncratic solutions that overfit to training conditions.
- On-policy methods (NPG) significantly outperform off-policy methods (DDPG, DDPGfD) on these high-dimensional tasks, suggesting that stability matters more than sample efficiency when scaling to complex manipulation.
- The asymptotic decay of demonstration influence in DAPG is important — it allows the policy to eventually surpass the demonstrator by relying purely on task reward, rather than being permanently anchored to demonstration behavior.
- This work established the ADROIT benchmark tasks that became widely used in subsequent dexterous manipulation research and influenced the design of later systems like OpenAI's Rubik's cube hand.
