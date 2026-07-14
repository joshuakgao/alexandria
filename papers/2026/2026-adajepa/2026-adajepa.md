---
title: "AdaJEPA: An Adaptive Latent World Model"
authors: [Ying Wang, Oumayma Bounou, Yann LeCun, Mengye Ren]
year: 2026
tags: [world-models, reinforcement-learning]
url: "https://arxiv.org/abs/2606.32026"
date_ingested: 2026-07-06
---

# AdaJEPA: An Adaptive Latent World Model

![[2026-adajepa-thumbnail.png]]

## Research gap
Latent world models (JEPAs) are kept frozen at test time after training. When their predictions become inaccurate — due to visual changes (noise, lighting, color), dynamics shifts (friction, mass), or layout changes — MPC planning optimizes the wrong objective, and even small one-step errors compound over the planning horizon. No prior work adapts a JEPA world model during planning.

## Contributions
- AdaJEPA: a test-time adaptation framework that operates within the closed loop of MPC, creating a plan–execute–adapt–replan loop
- Demonstrates that a single gradient step per MPC replanning step is sufficient for substantial planning improvement
- Comprehensive evaluation across four types of distribution shift: shape, visual, dynamics, and layout
- Shows the approach is agnostic to the underlying world model implementation (works across multiple JEPA variants)

## Method
AdaJEPA extends JEPA world models with a closed-loop adaptation mechanism during MPC:

1. **Plan** with the current world model by optimizing an action sequence to minimize latent goal-reaching cost over a horizon H
2. **Execute** the first action chunk and observe the resulting next state
3. **Adapt** the world model using the observed transition as a self-supervised signal — minimize the latent prediction error between the predicted and actual next-state encoding
4. **Replan** with the updated model

Key design choices:
- **Online buffer** (size N=5): stores recent transitions; two strategies — recent-N (most recent) or hard-N (largest prediction errors)
- **Adaptation loss**: same self-supervised latent prediction loss as pretraining, with stop-gradient on targets to prevent collapse
- **Adapted parameters**: only final layers of the visual encoder and predictor are updated, keeping adaptation lightweight (U=1 gradient step)
- **Per-episode reset**: each episode starts from the pretrained model with its own copy and buffer
- Compatible with both gradient-based (GD) and sampling-based (CEM) planners

## Datasets & evaluation
Evaluated on PushT and PointMaze benchmarks with four types of distribution shift:

- **Shape shifts** (PushObj): train on {T, L, Z, +}, test on OOD shapes {I, smallT, square} — AdaJEPA nearly doubles planning success on unseen shapes
- **Visual shifts**: Gaussian blur, salt-and-pepper noise, dark lighting, color changes — consistent gains on most corruptions
- **Dynamics shifts** (PointMaze-Medium): low mass (0.2×), high damping (20×) — improvements even on top of strong frozen baselines
- **Layout shifts**: 25 training mazes, 5 held-out mazes on 8×8 grids — up to +25% success rate on unseen layouts

Adaptation introduces negligible latency (~0.01s overhead per replan on H200) while reducing overall episode time by reaching goals in fewer replans.

## Limitations
- Evaluated only on relatively simple 2D environments (PushT, PointMaze); generalization to high-dimensional 3D tasks is untested
- Color shifts that change semantically meaningful features (e.g., distinguishing anchor from manipulated object) show modest gains — may require data augmentation or invariance regularization
- Per-episode reset means adaptation does not accumulate across episodes
- Only tested with goal-conditioned MPC planning; applicability to reward-based or policy-based control is unexplored
- Buffer size and adaptation hyperparameters (learning rate, number of steps) may require tuning per environment

## Key takeaways
- Latent world models should not remain frozen at deployment — even a single gradient step of self-supervised adaptation per MPC step substantially improves planning under distribution shift
- The plan–execute–adapt–replan loop creates a tight coupling between learning and planning: actions generate adaptation signal, and the updated model immediately improves subsequent planning
- Test-time adaptation is safe in-distribution: it yields large gains when the frozen model is suboptimal and does no harm when already near-optimal
- The approach is implementation-agnostic — consistent improvements across different JEPA variants (global features, spatial features, DINO-WM)
- Decoded rollouts after adaptation retain training-domain visual structure (e.g., unseen red block decoded as gray), suggesting adaptation exploits shared latent structure rather than learning entirely new representations
