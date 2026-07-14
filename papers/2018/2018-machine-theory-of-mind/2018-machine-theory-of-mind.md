---
title: "Machine Theory of Mind"
authors: [Neil C. Rabinowitz, Frank Perbet, H. Francis Song, Chiyuan Zhang, S. M. Ali Eslami, Matthew Botvinick]
year: 2018
venue: "ICML 2018"
tags: [multi-agent, reinforcement-learning, theory-of-mind]
url: "https://arxiv.org/abs/1802.07740"
date_ingested: 2026-07-12
---

# Machine Theory of Mind

![[2018-machine-theory-of-mind-thumbnail.png]]

## Research gap
Prior work on modeling other agents relied on hand-crafted models (inverse RL, Bayesian Theory of Mind, game theory) that assume agents are noisy-rational planners. These approaches lack scalability and require strong prior assumptions about agent structure, limiting their applicability to complex multi-agent settings.

## Contributions
- Introduces the ToMnet (Theory of Mind network), a neural network that uses meta-learning to build models of other agents from behavioral observations alone.
- Demonstrates that a learned observer can acquire a general prior over agent behavior and bootstrap to agent-specific predictions from few observations.
- Shows the system passes classic Theory of Mind tests, including the Sally-Anne false belief task.
- Frames Theory of Mind as a meta-learning problem rather than a hand-engineered inference problem.

## Method
The ToMnet consists of three modules:
1. **Character net** — consumes past behavioral trajectories of an agent to produce a character embedding (encoding stable traits like goals and preferences).
2. **Mental state net** — processes the current episode's observations to infer the agent's evolving mental state (beliefs, intentions).
3. **Prediction net** — combines character and mental state embeddings with the current state to predict next actions, successor representations, and beliefs.

Training uses meta-learning: the observer is trained across a population of agents, learning to rapidly form accurate predictions about novel agents from limited behavioral data. The architecture separates what is stable about an agent (character) from what changes within an episode (mental state).

## Datasets & evaluation
Experiments use gridworld environments of increasing complexity:
- Random and algorithmic agents to validate basic inference capabilities.
- Deep RL agents with varied reward functions, discount factors, and observation fields of view.
- POMDP settings with swap events to test false belief recognition (Sally-Anne paradigm).

Key results: The ToMnet successfully infers agent goals from few observations (few-shot inverse RL), distinguishes agent species from behavior alone, and correctly predicts that partially observable agents will act on false beliefs after unobserved environmental changes.

## Limitations
- Only tested in simple gridworld environments — scalability to complex, high-dimensional domains is undemonstrated.
- Single-agent POMDPs only; multi-agent interactions not explored.
- Does not attempt to align predictions with human judgments of mental states.
- Agents' hidden states do not carry across episodes, simplifying the modeling problem.

## Key takeaways
- Theory of Mind can be cast as a meta-learning problem, removing the need for hand-crafted agent models.
- The separation of character (stable traits) and mental state (episode-specific beliefs) is a powerful inductive bias for modeling other agents.
- A learned observer can discover that agents hold false beliefs without being explicitly programmed with the concept — this emerges from the structure of the meta-learning task.
- This approach opens paths toward interpretable AI (understanding agents via learned abstractions rather than weight inspection) and improved multi-agent coordination.
