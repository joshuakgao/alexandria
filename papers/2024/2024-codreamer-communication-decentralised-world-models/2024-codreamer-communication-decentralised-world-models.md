---
title: "CoDreamer: Communication-Based Decentralised World Models"
authors: [Edan Toledo, Amanda Prorok]
year: 2024
venue: "CoCoMARL Workshop, RLC 2024"
tags: [multi-agent, reinforcement-learning, world-models]
url: "https://arxiv.org/abs/2406.13600"
date_ingested: 2026-07-01
---

# CoDreamer: Communication-Based Decentralised World Models

![[2024-codreamer-communication-decentralised-world-models-thumbnail.png]]

## Research gap
Model-based RL methods like Dreamer achieve strong sample efficiency in single-agent settings, but their application to multi-agent environments is limited. Independent world models fail to capture inter-agent dynamics: partial observability, non-stationarity (other agents' changing policies), and cooperative reward structures that depend on joint behavior make single-agent world models inaccurate, producing unreliable imagined trajectories for policy training.

## Contributions
- IDreamer: a baseline adaptation of DreamerV3 to independent multi-agent learning with parameter sharing and agent-index conditioning
- CoDreamer: a two-level GNN-based communication system integrated into DreamerV3, with communication within world models (for better environment modeling) and within actor-critic networks (for better cooperation)
- A formal argument that CoDreamer is strictly more expressive than IDreamer — it can model the true reward and transition functions of Dec-MDPs, which IDreamer cannot guarantee
- Comprehensive evaluation across vector-based (VMAS) and pixel-based (Melting Pot) multi-agent environments showing statistically significant improvements over IDreamer and IPPO

## Method
CoDreamer extends DreamerV3 to multi-agent settings with two levels of communication:

**Level 1 — World model communication**: GNN layers (GAT V2) are added within the RSSM to allow agents' world models to exchange information during both training and imagination. Observations, recurrent states, and stochastic states are represented as graphs where nodes are agents and edges connect agents within communication range (Euclidean distance). The reward and terminal prediction heads use GNN communication; the decoder remains independent. This helps world models capture inter-agent dynamics, reducing non-stationarity and partial observability.

**Level 2 — Actor-critic communication**: Separate GNN layers serve as a torso for the actor and critic networks operating on imagined world model state graphs. This enables agents to share action and value prediction information during policy training. During imagination rollouts, the adjacency matrix from the starting graph is held fixed (nearby agents at the start are assumed most relevant for short-horizon predictions).

Both levels use parameter sharing across homogeneous agents, with one-hot agent indices concatenated to observations in non-visual environments. The framework follows Centralized Training with Decentralized Execution (CTDE) — during execution, agents communicate only with neighbors within range.

## Datasets & evaluation
- **VMAS** (vector-based, 3 tasks: Flocking, Discovery, Buzz Wire): CoDreamer outperforms IDreamer and IPPO on all aggregate metrics (median, IQM, mean) with minor CI overlap. Marginal trade-off in initial sample efficiency due to communication protocol learning overhead.
- **Melting Pot** (pixel-based, 4 tasks: Daycare, Cooperative Mining, 2x Collaborative Cooking): CoDreamer significantly outperforms IDreamer and IPPO with no CI overlap. IPPO fails completely (scores below 0.1 on all tasks) due to sample inefficiency with pixel observations at 500K steps. CoDreamer achieves near-maximum normalized scores in 25% of runs.
- Evaluation follows the protocol of Agarwal et al. (2021) with performance profiles, IQM, and 95% confidence intervals across multiple seeds.

## Limitations
- Evaluated only on homogeneous agent settings with parameter sharing; heterogeneous multi-agent scenarios are not tested
- The adjacency matrix is held fixed during imagination rollouts, which may become stale over longer horizons
- Communication range is environment-dependent and fixed; adaptive or learned communication topology is not explored
- Workshop paper with relatively small-scale experiments (500K environment steps); scaling to larger agent counts and longer training is unclear
- The added GNN communication layers increase model complexity and slightly reduce initial sample efficiency in vector-based environments
- Edge features (relative distances) are not updated during imagination, potentially degrading communication quality over imagined trajectories

## Key takeaways
- Independent world models fundamentally cannot capture inter-agent dependent dynamics — when rewards and transitions depend on joint behavior, imagined trajectories from independent models are unreliable, and policy learning in imagination fails
- GNN-based communication within world models is a natural and effective solution: it maintains decentralized execution while allowing agents to share information that improves environment modeling accuracy
- The two-level communication design (world model level for environment understanding, actor-critic level for cooperation) separates concerns effectively — the world model learns environment-grounded communication independent of any specific policy
- The benefits of communication are most pronounced in pixel-based, partially observable environments (Melting Pot) where the gap between CoDreamer and baselines is large and statistically significant
- CoDreamer represents an important step toward scaling model-based RL to multi-agent settings, combining the sample efficiency of Dreamer with the communication structures needed for cooperative tasks
