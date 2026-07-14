---
title: "Mastering Atari with Discrete World Models"
authors: [Danijar Hafner, Timothy Lillicrap, Mohammad Norouzi, Jimmy Ba]
year: 2021
venue: "ICLR 2021"
tags: [reinforcement-learning, world-models]
url: "https://arxiv.org/abs/2010.02193"
date_ingested: 2026-06-28
---

# Mastering Atari with Discrete World Models

![[2021-dreamerv2-thumbnail.png]]

## Research gap

Despite the promise of world models for sample-efficient RL, no model-based agent had achieved human-level performance on the Atari benchmark — the standard test for deep RL. Prior world model approaches (SimPLe, PlaNet, DreamerV1) used Gaussian latent variables and were not accurate enough on discrete, visually complex Atari games. Meanwhile, MuZero achieved strong Atari results but required vast compute (2+ months per game on GPU) and was not publicly available. The gap between model-based and model-free agents on the most competitive benchmarks remained open.

## Contributions

- Introduces **DreamerV2**, the first agent to achieve human-level performance on the Atari-55 benchmark by learning behaviors purely within a separately trained world model.
- Replaces Gaussian latent variables with **categorical latent variables** (32 categoricals × 32 classes = 1024-dimensional sparse binary vectors), trained via straight-through gradients, significantly improving world model accuracy.
- Introduces **KL balancing** — asymmetric learning rates for the prior (α=0.8) vs. posterior (1-α=0.2) in the KL loss — encouraging the prior to match the posterior rather than the posterior collapsing toward a poorly trained prior.
- Outperforms top single-GPU model-free agents (IQN, Rainbow, DQN) at 200M frames using only a single GPU and single environment instance, with the world model imagining 468B compact states (10,000× more than real interactions).

## Method

DreamerV2 retains Dreamer's three-phase structure (world model learning → behavior learning in imagination → environment interaction) with key modifications:

**World model changes from DreamerV1:**
- **Categorical latents**: The RSSM stochastic state is now a vector of 32 categorical variables with 32 classes each, instead of a diagonal Gaussian. Optimized via straight-through gradients (one-hot sample + softmax gradient). Benefits: categorical priors can perfectly fit mixture posteriors; sparse binary representations may generalize better; better inductive bias for non-smooth Atari dynamics.
- **KL balancing**: The KL loss uses different learning rates for prior vs. posterior — 80% of the gradient updates the prior toward the posterior, 20% regularizes the posterior. This prevents the common failure mode where a poorly trained prior drags down representation quality.
- **Discount predictor**: A Bernoulli predictor for episode termination, enabling the agent to account for episode boundaries during imagination.

**Actor-critic changes:**
- Combines **Reinforce gradients** (unbiased, high-variance) with **straight-through dynamics gradients** (biased, low-variance). For Atari: pure Reinforce (ρ=1); for continuous control: pure dynamics backpropagation (ρ=0).
- Entropy regularization of the actor for exploration.
- Target network for critic (updated every 100 steps) for stable value learning.

**Scale:** 20M world model parameters, 1M each for actor and critic. Imagines 2,500 latent trajectories in parallel on a single GPU. Reaches 200M environment frames in under 10 days.

## Datasets & evaluation

**Atari-55 benchmark** with sticky actions (Machado et al., 2018 protocol): 55 games, action repeat 4, 108K step time limit, full action space, no life information, no frame stacking.

**Baselines:** IQN, Rainbow, C51, DQN (scores from Dopamine framework with sticky actions).

**Scoring metrics** (paper recommends clipped record mean):
- Gamer median: DreamerV2 2.15 vs. Rainbow 1.47, IQN 1.29
- Gamer mean: DreamerV2 11.33 vs. Rainbow 9.12, IQN 8.85
- Clipped record mean: DreamerV2 0.28 vs. IQN 0.21, Rainbow 0.17

**Key ablation results:**
- Categorical > Gaussian latents: wins on 42/55 games
- KL balancing: wins on 44/55 games
- Image gradients critical: removing them drops clipped record mean from 0.25 to 0.01
- Reward gradients optional: removing them is neutral or slightly beneficial on some games, suggesting general representations outperform reward-specific ones

**Continuous control:** Also applicable to DM Control tasks with continuous actions, learning humanoid stand-up and walking from pixels.

## Limitations

- Single-task only — a separate world model and policy are trained per game; no transfer across games.
- Single GPU but still requires ~10 days per game at 200M frames.
- Struggles on games where critical objects are tiny (Video Pinball — ball is 1 pixel), since reconstruction loss doesn't prioritize task-relevant visual features.
- The optimal gradient mixing (Reinforce vs. dynamics backpropagation) differs between discrete and continuous action spaces, requiring domain-specific hyperparameter choices.
- Image reconstruction allocates model capacity to task-irrelevant visual details.

## Key takeaways

- **Categorical latent variables are a better inductive bias than Gaussians for world models** — the sparse, discrete representations enable more accurate dynamics prediction, especially for environments with non-smooth transitions (entering rooms, objects appearing/disappearing). This finding influenced representation learning beyond RL.
- **KL balancing is a general technique for sequential VAEs**: by prioritizing prior accuracy over posterior regularization, the learned dynamics become reliable enough for long-horizon imagination. This addresses a fundamental failure mode of latent variable models where the prior never catches up to the posterior.
- DreamerV2 closes the model-based vs. model-free gap on the hardest standard benchmark, demonstrating that world models are not just sample-efficient alternatives but can match or exceed model-free methods in asymptotic performance — the 10,000× amplification of experience through imagination is the mechanism.
- The finding that reward gradients are unnecessary (or even harmful) for representation learning suggests world models benefit from learning general environment representations rather than reward-specific ones — a principle that connects to self-supervised learning approaches like V-JEPA.
