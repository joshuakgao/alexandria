---
title: "Exploration by Random Network Distillation"
authors: [Yuri Burda, Harrison Edwards, Amos Storkey, Oleg Klimov]
year: 2018
venue: "ICLR 2019"
tags: [reinforcement-learning]
url: "https://arxiv.org/abs/1810.12894"
date_ingested: 2026-07-08
---

# Exploration by Random Network Distillation

![[2018-rnd-exploration-random-network-distillation-thumbnail.png]]

## Research gap
Deep RL methods fail on environments with sparse rewards because random exploration is insufficient to discover rewarding states. Existing exploration bonuses based on forward dynamics prediction suffer from the "noisy TV" problem — agents get attracted to stochastic transitions that are inherently unpredictable, rather than genuinely novel states. Methods that instead measure prediction improvement (information gain) avoid this issue but are computationally expensive and difficult to scale to large numbers of parallel environments.

## Contributions
- Random Network Distillation (RND): an exploration bonus defined as the prediction error of a trained predictor network attempting to match the output of a fixed, randomly initialized target network on the current observation. This is deterministic by construction, avoiding the noisy TV problem.
- A method for combining intrinsic and extrinsic reward streams with different characteristics (episodic vs. non-episodic, different discount factors) using dual value heads in PPO.
- State-of-the-art performance on Montezuma's Revenge without demonstrations or access to the underlying game state — the first method to achieve better than average human performance under these constraints.
- Extensive ablations isolating the contributions of non-episodic intrinsic returns, discount factor choices, scale of parallel environments, and recurrence.

## Method
RND uses two neural networks: a fixed randomly initialized **target network** f: O -> R^k and a **predictor network** f_hat: O -> R^k trained via gradient descent to minimize MSE ||f_hat(x) - f(x)||^2 on observations collected by the agent. The prediction error serves as the intrinsic reward — it is high for observations dissimilar to those previously encountered (epistemic uncertainty) and naturally decreases as the agent accumulates experience in familiar states.

Key design choices:
- **Deterministic prediction target**: Since the target network is fixed and deterministic, the prediction problem has no aleatoric uncertainty — eliminating the noisy TV problem that plagues forward dynamics prediction bonuses.
- **Non-episodic intrinsic returns**: Treating intrinsic rewards as non-episodic (not truncated at game over) encourages risk-taking exploration, since the agent values future novel states regardless of whether they occur in the current episode.
- **Dual value heads**: Two separate value functions V_E and V_I are trained for extrinsic and intrinsic reward streams respectively, then combined as V = V_E + V_I. This allows different discount factors (gamma_E = 0.999, gamma_I = 0.99) and episodic vs. non-episodic treatment for each stream.
- **Observation normalization**: Running whitening of observations is critical since the target network's frozen parameters cannot adapt to input scale. Observations are clipped to [-5, 5] after normalization.
- **Reward normalization**: Intrinsic rewards are divided by a running estimate of the standard deviation of intrinsic returns, keeping the bonus on a consistent scale across environments and time.

The method integrates with PPO and scales efficiently with parallel environments, requiring only a single forward pass of the predictor/target networks per batch.

## Datasets & evaluation
- **Primary benchmark**: Montezuma's Revenge (Atari) — famously difficult for deep RL due to sparse rewards requiring hundreds of steps of optimal play between rewards.
- **Additional hard exploration games**: Gravitar, Pitfall!, Private Eye, Solaris, Venture.
- **Pure exploration**: Without any extrinsic reward, RND agents consistently find >50% of rooms in Montezuma's Revenge. Non-episodic intrinsic returns significantly outperform episodic.
- **Combined rewards**: Best agent (RNN, 1024 parallel environments, gamma_E=0.999) achieves mean return of 10,070 on Montezuma's Revenge, with one run reaching mean return of 14,415. Often finds 22/24 rooms and occasionally completes the first level (best single-episode return: 17,500).
- **Comparison to baselines**: Significantly outperforms PPO without exploration bonus on Montezuma's Revenge (8,152 vs. 2,497) and Venture (1,859 vs. 0). Outperforms forward dynamics exploration bonus on Montezuma's Revenge, Private Eye, and Solaris.
- **Scaling**: Performance improves with more parallel environments (32 -> 128 -> 1024), with RNN policies benefiting more from scale than CNN policies.

## Limitations
- Performance on Pitfall! remains at zero reward — the extrinsic reward is so sparse that even RND exploration cannot discover it.
- The agent exhibits "dancing with skulls" behavior — once familiar extrinsic rewards are exhausted, it gravitates toward dangerous interactions that are novel but not necessarily useful for task completion.
- RNN policies are less stable than CNN counterparts despite the partial observability of Atari games, though RNNs outperform CNNs at higher discount factors and larger scale.
- The method relies on observation-space novelty, which may not capture task-relevant novelty in environments with high-dimensional observations where many irrelevant dimensions change frequently.
- Predictor network batch size must be carefully controlled when scaling parallel environments to maintain consistent intrinsic reward decay rates.

## Key takeaways
- Prediction error against a fixed random target is a surprisingly effective novelty signal — it sidesteps the noisy TV problem by making the prediction target deterministic, while remaining trivial to implement (just one extra forward pass per batch).
- The connection to Bayesian uncertainty quantification provides theoretical grounding: RND prediction error approximates the predictive variance of an ensemble, interpreting distillation error as uncertainty in predicting a constant zero function.
- Non-episodic intrinsic rewards are critical for exploration — they prevent the agent from being overly risk-averse about game overs, since the value of future novel states persists across episode boundaries.
- Dual value heads for separate reward streams enable flexible combination of rewards with different temporal characteristics (discount factors, episodic vs. non-episodic), a generally useful architectural pattern for multi-objective RL.
- Scale matters: more parallel environments lead to better exploration and higher returns, suggesting that exploration methods must be designed to scale efficiently with compute.
