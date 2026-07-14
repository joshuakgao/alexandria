---
title: "Dream to Control: Learning Behaviors by Latent Imagination"
authors: [Danijar Hafner, Timothy Lillicrap, Jimmy Ba, Mohammad Norouzi]
year: 2020
venue: "ICLR 2020"
tags: [reinforcement-learning, world-models]
url: "https://arxiv.org/abs/1912.01603"
date_ingested: 2026-06-28
---

# Dream to Control: Learning Behaviors by Latent Imagination

![[2020-dreamer-thumbnail.png]]

## Research gap

Prior model-based RL agents either use derivative-free optimization (online planning via CEM in PlaNet, evolution strategies in World Models) which is sample-inefficient and cannot leverage the analytic gradients offered by neural network dynamics, or use short-horizon imagination without value estimation, leading to shortsighted behaviors. No method efficiently learns long-horizon behaviors from images purely through latent imagination with analytic gradient propagation through learned dynamics.

## Contributions

- Introduces **Dreamer**, an RL agent that learns long-horizon behaviors from images purely by latent imagination — propagating analytic gradients of learned state values back through trajectories imagined in a learned world model's compact latent space.
- Proposes a novel actor-critic algorithm for latent imagination: the value model estimates rewards beyond the imagination horizon via λ-returns (Vλ), while the action model maximizes values by backpropagating through the differentiable dynamics.
- Exceeds both model-based (PlaNet) and model-free (D4PG, A3C) agents on 20 visual control tasks from DeepMind Control Suite in data-efficiency, computation time, and final performance — with a single set of hyperparameters.
- Demonstrates that value prediction makes the agent robust to imagination horizon length, solving long-horizon tasks (acrobot, hopper) where horizon-only methods fail.

## Method

Dreamer operates in three interleaved phases:

**1. World model learning** — learns latent dynamics from experience:
- **Representation model** p(s_t | s_{t-1}, a_{t-1}, o_t): encodes observations into compact model states via a Recurrent State Space Model (RSSM) with both deterministic and stochastic components.
- **Transition model** q(s_t | s_{t-1}, a_{t-1}): predicts future states without observations.
- **Reward model** q(r_t | s_t): predicts scalar rewards from states.
- Trained via ELBO with image reconstruction (observation model), reward prediction, and KL regularization between representation and transition models.

**2. Behavior learning by latent imagination:**
- Starting from true model states of experience sequences, imagines trajectories forward for H steps using the transition model and action model.
- **Value model** v_ψ(s_τ): estimates expected discounted returns from each imagined state, trained to regress λ-returns (Vλ) — exponentially-weighted averages of k-step returns that balance bias and variance.
- **Action model** q_φ(a_τ | s_τ): outputs tanh-transformed Gaussian actions via reparameterization, trained by backpropagating analytic gradients of Vλ estimates through the differentiable dynamics chain: values → rewards → states → actions.
- All computation happens in the compact latent space — no image generation needed during imagination, enabling thousands of parallel imagined trajectories.

**3. Environment interaction** — executes learned actions, collects experience into dataset.

Key design choices:
- Reparameterized sampling for both actions (tanh Gaussian) and latent states enables end-to-end gradient flow.
- λ-returns (Vλ) combine multi-step reward sums with value estimates, making the agent robust to imagination horizon H.
- Straight-through gradients for discrete actions.

## Datasets & evaluation

**Environment**: 20 visual control tasks from DeepMind Control Suite — continuous control from 64×64 RGB images. Tasks include cartpole, cheetah, walker, hopper, quadruped, acrobot, reacher, finger, cup, and pendulum domains with varying difficulty.

**Baselines**: PlaNet (model-based, online planning), D4PG (model-free, distributed), A3C (model-free, actor-critic). Model-free baselines trained for 10⁸–10⁹ steps; Dreamer and PlaNet for 5×10⁶ steps.

**Key results:**
- Dreamer achieves average return of 823 across 20 tasks at 5×10⁶ steps, vs. PlaNet at 332 (same budget) and D4PG at 786 (at 10⁸ steps — 20× more data).
- Solves long-horizon tasks (acrobot swingup, hopper hop) where PlaNet and no-value ablations fail completely — demonstrating the critical role of value estimation beyond the imagination horizon.
- Robust to imagination horizon H: performance stable from H=5 to H=45, while no-value and PlaNet degrade sharply.
- Reconstruction-based representation learning outperforms reward-only and contrastive objectives in most tasks, though contrastive is competitive in some.
- Approximately 2× faster in wall-clock time than PlaNet (no online planning needed at test time).

## Limitations

- Evaluated only on DeepMind Control Suite — relatively simple visual environments with consistent physics; generalization to visually complex or stochastic real-world environments not tested.
- Continuous action spaces only; discrete action domains (Atari) not evaluated (addressed in DreamerV2).
- Image reconstruction as representation learning objective is computationally expensive and may allocate model capacity to task-irrelevant visual details.
- World model errors compound over long imagination horizons — while λ-returns mitigate this, fundamentally bounded by model accuracy.
- Single-task learning; no transfer or multi-task evaluation.

## Key takeaways

- Dreamer demonstrates that **analytic gradient propagation through learned dynamics** is practical and superior to both derivative-free planning (PlaNet/CEM) and model-free learning (D4PG/A3C) — combining the data efficiency of model-based methods with the asymptotic performance of model-free methods.
- The combination of latent imagination with actor-critic learning is the key innovation: the value model extends effective planning beyond any fixed horizon, while the action model amortizes planning into a policy that requires no search at test time. This resolves the shortsightedness problem that limited prior model-based approaches.
- Dreamer operationalizes Ha & Schmidhuber's dream training concept at scale: where World Models (2018) used evolution strategies on a linear controller in a two-stage process, Dreamer uses differentiable actor-critic learning in a jointly trained latent space, achieving far stronger results on harder tasks.
- The RSSM world model (deterministic + stochastic state) provides accurate enough multi-step predictions for gradient-based policy optimization — the model doesn't need to be perfect, just good enough for the gradients to point in useful directions.
