---
title: "On the Utility of Learning about Humans for Human-AI Coordination"
authors: [Micah Carroll, Rohin Shah, Mark Ho, Thomas Griffiths, Sanjit Seshia, Pieter Abbeel, Anca Dragan]
year: 2019
venue: "NeurIPS 2019"
tags: [reinforcement-learning, multi-agent]
url: "https://arxiv.org/abs/1910.05789"
date_ingested: 2026-07-01
---

# On the Utility of Learning about Humans for Human-AI Coordination

![[2019-utility-learning-humans-human-ai-coordination-thumbnail.png]]

## Research gap
Self-play and population-based training produce agents that coordinate well with copies of themselves but fail when paired with humans. In competitive games, human suboptimality only helps the AI agent, but in collaborative settings, unexpected human behavior can lead to catastrophic coordination failure. No prior work had systematically demonstrated this gap in deep RL or evaluated whether incorporating human models into training could close it.

## Contributions
- A simplified Overcooked environment designed to test challenging coordination between agents, with five layouts targeting different coordination difficulties (low-level motion, high-level strategy, forced cooperation)
- Empirical demonstration that self-play and population-based training agents perform drastically worse when paired with humans than with themselves, across both simulated human models and real users
- Evidence that training with even a simple behavior-cloned human model significantly improves human-AI coordination compared to self-play methods
- A user study (60 participants) confirming the simulation findings: PPO agents trained with human models significantly outperform self-play and PBT agents when paired with real humans

## Method
The authors compare three categories of agents in a cooperative Overcooked environment:

**Agents trained with themselves**: Self-play (SP) trains a PPO agent against copies of itself. Population-based training (PBT) trains a population of agents that play with each other, with evolutionary selection. Coupled planning (CP) computes optimal joint plans using hierarchical A* search, replanning after each step.

**Human model**: Human-human gameplay data (~16 trajectories per layout, 18k timesteps total) is split into two subsets. Behavior cloning (BC) trains a model on one subset; a held-out proxy human (H_proxy) trained on the other subset serves as the evaluation partner.

**Agents trained with human models**: PPO_BC embeds the BC model in the environment as the partner and trains a PPO policy against it, with an annealing schedule from self-play to human-model training. P_BC uses hierarchical A* planning assuming the BC model as the partner.

All agents are evaluated by pairing with H_proxy (simulation) and with real humans (user study, N=60 on Amazon Mechanical Turk).

## Datasets & evaluation
- **Environment**: Simplified Overcooked with 5 layouts (Cramped Room, Asymmetric Advantages, Coordination Ring, Forced Coordination, Counter Circuit), each targeting different coordination challenges
- **Human data**: ~16 human-human trajectories per layout collected via a web interface
- **Simulation results**: SP and PBT agents achieve high rewards with themselves but dramatically underperform when paired with H_proxy. PPO_BC significantly outperforms self-play methods and approaches the gold-standard performance (PPO trained directly with H_proxy)
- **User study**: ANOVA confirms significant main effect of agent type on reward (F(2,224)=6.49, p<.01). PPO_BC significantly outperforms SP (p=.01) and PBT (p<.01) with Tukey HSD corrections. In some layouts, PPO_BC even outperforms human-human performance
- **Planning results**: Coupled planning shows the same trend — excellent self-play performance but poor human coordination. However, the model-based planner is brittle: inaccuracies in the BC model cause it to get stuck in loops

## Limitations
- The Overcooked environment is relatively simple compared to real-world coordination tasks; generalization to more complex settings is unclear
- The behavior-cloned human model is a crude approximation — it gets stuck in loops and requires rule-based unsticking mechanisms
- The user study uses a between-subjects design with 20 participants per condition, limiting statistical power for per-layout analysis
- The planning approach is only feasible on two of five layouts due to computational complexity
- Human adaptivity partially masks the benefits of human-aware agents: real humans can figure out opaque self-play strategies, reducing the gap observed in simulation
- No exploration of online human model adaptation — the human model is fixed during training

## Key takeaways
- Collaboration is fundamentally different from competition for AI: in competitive games, human suboptimality helps the AI, but in cooperative games it causes catastrophic coordination failure when the AI assumes optimal or self-similar partners
- Even a crude behavior-cloned human model is better than no human model — the distributional shift from self-play to human partners is more damaging than the inaccuracy of a simple human model
- The Overcooked-AI environment became a widely-adopted benchmark for human-AI coordination research, influencing subsequent work on cooperative AI, theory of mind, and ad-hoc teamwork
- These results are a cautionary tale for deploying collaborative AI: agents that excel in self-play evaluations may fail dramatically when paired with real humans
