---
title: "The Surprising Effectiveness of PPO in Cooperative Multi-Agent Games"
authors: [Chao Yu, Akash Velu, Eugene Vinitsky, Jiaxuan Gao, Yu Wang, Alexandre Bayen, Yi Wu]
year: 2022
venue: "NeurIPS 2022"
tags: [reinforcement-learning, multi-agent]
url: "https://arxiv.org/abs/2103.01955"
date_ingested: 2026-07-04
---

# The Surprising Effectiveness of PPO in Cooperative Multi-Agent Games

![[2022-mappo-surprising-effectiveness-ppo-cooperative-thumbnail.png]]

## Research gap
Off-policy methods (QMix, MADDPG, value decomposition) dominated cooperative multi-agent RL benchmarks, while PPO was considered too sample-inefficient for multi-agent settings. However, no systematic study had examined whether PPO's underperformance was fundamental or simply a consequence of suboptimal implementation and hyperparameter choices when transferring single-agent practices to multi-agent domains.

## Contributions
- Demonstrates that **MAPPO** (PPO with centralized value function) and **IPPO** (independent PPO) achieve competitive or superior performance to state-of-the-art off-policy methods across four cooperative MARL benchmarks (MPE, SMAC, Google Research Football, Hanabi) with minimal hyperparameter tuning and no domain-specific algorithmic modifications.
- Shows that PPO achieves these results with **comparable sample efficiency** to off-policy methods, contradicting the common belief that on-policy methods are significantly less sample-efficient in multi-agent settings.
- Identifies and analyzes **five critical implementation/hyperparameter factors** for PPO in multi-agent settings: value normalization, value function input representation, training data usage (epochs and mini-batches), PPO clipping strength, and batch size.
- Provides concrete, actionable best-practice suggestions for each factor, establishing MAPPO as a strong baseline for cooperative MARL.
- Open-sources the implementation at https://github.com/marlbenchmark/on-policy.

## Method
**MAPPO:** PPO with a centralized value function that takes global state information as input during training (CTDE paradigm). Policy and value function are separate neural networks. In homogeneous-agent settings, parameters are shared across agents.

**IPPO:** PPO where both policy and value function use only local observations — fully decentralized.

**Value function input representations (SMAC-specific):**
- **CL (Concatenated Local):** concatenation of all agents' local observations — high-dimensional, scales poorly with agent count.
- **EP (Environment-Provided):** global state from the environment — misses agent-specific information (agent ID, available actions).
- **AS (Agent-Specific):** concatenation of EP state and agent's local observation — comprehensive but redundant.
- **FP (Feature-Pruned):** AS with overlapping features removed — best balance of information and dimensionality.

**Key implementation practices:** GAE with advantage normalization, value clipping, orthogonal initialization, recurrent policies (GRU) where needed.

## Datasets & evaluation
**Benchmarks:**
- **MPE (Multi-Agent Particle Environment):** 3 cooperative tasks (Spread, Reference, Comm). MAPPO matches/exceeds QMix and MADDPG.
- **SMAC (StarCraft Multi-Agent Challenge):** 23 maps ranging from easy to super-hard. MAPPO achieves performance comparable or superior to QMix and RODE in the vast majority of maps.
- **Google Research Football:** 6 academy scenarios. MAPPO dramatically outperforms QMix (e.g., 88% vs. 8% on 3v1) and exceeds CDS and TiKick on most scenarios.
- **Hanabi:** 2–5 player full-scale games. MAPPO matches or exceeds SAD and VDN, with centralized value function becoming critical as agent count grows.

**Key ablation findings:**
- **Value normalization:** Always helps, critical when return ranges are large.
- **Training epochs:** 5–10 epochs for hard tasks, 15 for easy. Too many epochs degrade performance (hypothesized: non-stationarity amplification).
- **Mini-batches:** No mini-batch splitting (1 batch) is best; 4 mini-batches causes failure on hard maps.
- **Clipping ε:** Keep under 0.2; ε=0.05 is most stable but slower; large ε (0.3–0.5) causes suboptimal convergence.
- **Batch size:** Critical threshold exists; below it performance collapses; above it, diminishing returns and worsened sample efficiency.

## Limitations
- All benchmarks use **discrete action spaces** — continuous action MARL is not studied.
- All settings are **cooperative with shared rewards** — competitive and mixed-motive games are not evaluated.
- Most benchmarks have **homogeneous agents** — heterogeneous agent settings are underexplored.
- The study is **purely empirical** — no theoretical analysis of why PPO works well in multi-agent settings.
- Ablations are conducted primarily on SMAC and MPE — transferability of suggestions to other domains is assumed but not verified.
- Single desktop machine (1 GPU) — whether findings hold at massive distributed training scales (as in OpenAI Five, AlphaStar) is not tested.

## Key takeaways
- **PPO is a strong cooperative MARL baseline** — the conventional wisdom that off-policy methods dominate multi-agent settings is wrong when PPO is properly configured. This has important implications for practitioners who may default to more complex off-policy algorithms unnecessarily.
- **Non-stationarity requires conservative updates:** The key insight linking all five suggestions is that multi-agent settings amplify non-stationarity (each agent's policy changes affect all others' learning signals). Conservative PPO configurations (fewer epochs, no mini-batching, small ε) that limit per-update policy change improve stability — the opposite of single-agent best practices where aggressive sample reuse is standard.
- **Centralized value functions matter at scale:** IPPO matches MAPPO with few agents but falls behind as agent count grows (especially visible in 5-player Hanabi), confirming that global information in the value function is increasingly important for credit assignment in larger teams.
- **MAPPO as community standard:** This paper established MAPPO as the default PPO-based MARL algorithm, widely adopted in subsequent work including CoDreamer (which extends it with world models) and Lucy-SKG (which uses PPO for Rocket League). The open-source implementation became a standard benchmark.
