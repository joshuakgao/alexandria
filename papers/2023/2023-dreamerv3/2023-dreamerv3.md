---
title: "Mastering Diverse Domains through World Models"
authors: [Danijar Hafner, Jurgis Pasukonis, Jimmy Ba, Timothy Lillicrap]
year: 2023
venue: "Nature 2025"
tags: [reinforcement-learning, world-models]
url: "https://arxiv.org/abs/2301.04104"
date_ingested: 2026-06-28
---

# Mastering Diverse Domains through World Models

![[2023-dreamerv3-thumbnail.png]]

## Research gap

Reinforcement learning algorithms require significant hyperparameter tuning when applied to new domains — continuous vs. discrete actions, sparse vs. dense rewards, visual vs. proprioceptive inputs, 2D vs. 3D environments all demand different configurations. No single algorithm with fixed hyperparameters could match specialized expert methods across diverse domains. The Minecraft diamond challenge — requiring long-horizon exploration in an open world with sparse rewards — remained unsolved without human data or curricula.

## Contributions

- Introduces **DreamerV3**, a general RL algorithm that outperforms specialized expert methods across over 150 tasks spanning 8 diverse domains using a single fixed set of hyperparameters.
- Proposes robustness techniques — **symlog predictions**, **return normalization with percentile-based scaling**, **symexp twohot distributions** for critic, and **free bits with separate dynamics/representation losses** — that eliminate the need for domain-specific tuning.
- First algorithm to **collect diamonds in Minecraft from scratch** without human data or curricula, solving a long-standing challenge in AI.
- Demonstrates predictable scaling: larger models achieve higher scores and require less environment interaction, offering a clear path to improved performance.

## Method

DreamerV3 retains the three-phase Dreamer architecture (world model → imagination actor-critic → environment interaction) with robustness techniques that enable fixed hyperparameters across domains:

**World model (RSSM):**
- Same categorical latent variables as DreamerV2 (32 categoricals × 32 classes), with straight-through gradients.
- Splits the KL loss into separate **dynamics loss** (trains prior toward posterior) and **representation loss** (trains posterior toward prior) with asymmetric weights (βdyn=1, βrep=0.1). **Free bits** (clipping both losses below 1 nat) prevents them from dominating when already well-minimized, focusing learning on the prediction loss.
- 1% uniform mixture in categorical distributions prevents deterministic collapse and KL spikes.
- **Symlog transformation** on vector observations prevents large inputs from destabilizing the reconstruction/representation trade-off.

**Critic:**
- Predicts the **distribution of returns** (not just the mean) via symexp twohot encoding — a categorical distribution over exponentially spaced bins that decouples gradient magnitude from target magnitude.
- Regularized toward an exponential moving average of its own parameters (instead of periodic target network updates).
- Applied to both imagined trajectories and replay buffer trajectories for improved value prediction.

**Actor:**
- **Return normalization**: divides returns by the 5th-to-95th percentile range (EMA-smoothed), with a floor of 1 to avoid amplifying noise under sparse rewards. This preserves reward frequency information unlike advantage normalization.
- Uses Reinforce estimator for both discrete and continuous actions (unlike DreamerV2's domain-specific gradient mixing).
- Entropy regularizer with fixed scale η=3×10⁻⁴ across all domains.

**Symlog/symexp predictions:**
- symlog(x) = sign(x) · ln(|x| + 1): compresses large values while preserving sign and approximating identity near zero.
- Used for decoder targets, reward predictor, and critic, enabling robust learning across reward scales without target normalization.

## Datasets & evaluation

**8 domains, 150+ tasks:**
- **Atari** (57 games, 200M frames): outperforms MuZero with far less compute, outperforms Rainbow and IQN.
- **DM Control Suite** (20 tasks, continuous, 1M steps): matches or exceeds specialized methods.
- **ProcGen** (16 games, 50M frames): matches tuned PPG, outperforms Rainbow.
- **DMLab** (30 tasks, 3D, 100M steps): exceeds IMPALA and R2D2+ at 1B steps — over 1000% data-efficiency gain.
- **Minecraft Diamond** (1 task, 100M steps): first algorithm to collect diamonds from scratch.
- **Atari 100K** (26 tasks, low-data regime): competitive with specialized methods.
- **BSuite** (23 tasks): diagnostic suite.
- **Proprio Control** (18 tasks): non-visual continuous control.

**Minecraft results:** Dreamer reliably discovers diamonds; PPO, Rainbow, and IMPALA fail to progress past iron pickaxe. The task requires ~20,000 steps of sequential subgoals (collect wood → craft tools → mine stone → mine iron → smelt → mine diamond).

**Ablations:**
- Removing symlog, return normalization, symexp twohot, or KL balance/free bits each substantially degrades performance. Removing all robustness techniques together causes failure.
- Larger models (12M → 200M → 400M parameters) consistently improve performance and reduce sample requirements.
- Replay ratio scaling (0.125 to 2.0) shows predictable improvement with more gradient steps per environment step.

## Limitations

- Minecraft diamond is solved but not reliably — success rate is meaningful but not 100%.
- Still requires ~100M environment steps for Minecraft, far more than human-like sample efficiency.
- Image reconstruction remains the representation learning objective — potentially wasteful for visually complex environments.
- Single-task training only; no multi-task or transfer evaluation despite the "general algorithm" framing.
- Fixed hyperparameters work across tested domains but may not generalize to entirely novel domain types (e.g., multi-agent, language-conditioned).
- Reinforce gradients for continuous actions may be suboptimal compared to reparameterized gradients used in DreamerV1.

## Key takeaways

- **Robustness techniques are the key to generality**: symlog predictions, percentile return normalization, free bits, and distributional value functions collectively eliminate the need for per-domain tuning. The insight is that scale-invariant objectives (decoupling gradient magnitudes from target magnitudes) are more important than domain-specific architectural choices.
- **The Dreamer lineage demonstrates compounding architectural insights**: V1 introduced differentiable imagination, V2 added categorical latents and KL balancing, V3 adds scale-invariant robustness — each generation solves a broader class of problems with the same hyperparameters.
- **Minecraft diamond from scratch** is a landmark result: it requires ~20K-step sequential plans with sparse terminal rewards in a procedurally generated 3D world — exactly the kind of long-horizon, sparse-reward, open-world problem that was thought to require human demonstrations or shaped curricula.
- **Predictable scaling** (larger models → better performance with less data) suggests world model-based RL may follow scaling laws analogous to those in language modeling, offering a clear path to continued improvement through compute investment.
