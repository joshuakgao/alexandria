---
title: "Dreaming of Others: Latent Teammate Modeling in World Models for Multi-Agent Reinforcement Learning"
authors: [Tomas Leroy-Stone]
year: 2026
venue: "WMW 2026 Workshop"
tags: [world-models, multi-agent, reinforcement-learning]
url: "https://arxiv.org/abs/2605.31361"
date_ingested: 2026-07-04
---

# Dreaming of Others: Latent Teammate Modeling in World Models for Multi-Agent Reinforcement Learning

![[2026-dreaming-of-others-latent-teammate-modeling-thumbnail.png]]

## Research gap
World models like DreamerV3 have demonstrated strong generalization and sample efficiency in single-agent RL, but their application to cooperative MARL is limited by an inability to handle teammate-induced uncertainty. Existing multi-agent world model extensions (MA-Dreamer, CoDreamer, GAWM) rely on shared imagination, centralized policies, or explicit communication — sidestepping the challenge of partial observability over teammates. Treating teammates as undifferentiated noise induces apparent non-stationarity as partners change policies.

## Contributions
- Proposes treating teammates as structured, learnable latent processes within an agent's world model rather than as exogenous noise.
- Introduces an architecture that factorizes the RSSM latent state into environment (z^env) and teammate (z^team) components.
- Adds an auxiliary Theory-of-Mind (ToM) head that infers latent embeddings of partner behavior (character, intent, predicted actions) from partial trajectories.
- Outlines how teammate latents can condition actor and critic during imagination, enabling zero-shot and few-shot coordination with unseen partners.
- Proposes an evaluation protocol across MPE, Overcooked-AI, and Melting Pot benchmarks.

## Method
- **Factorized RSSM**: The stochastic latent z_t is split into z_t^env (environment dynamics) and z_t^team (inferred teammate behavior). An observation decoder reconstructs x̂_t from z_t^env; a teammate-policy decoder maps z_t^team to a predictive distribution π̂_t^j over the teammate's next action.
- **ToM objective**: Calibrated cross-entropy between predicted and observed teammate actions, plus a KL term for temporal consistency of inferred teammate latents. Added to the standard Dreamer loss with weight λ_ToM.
- **Imagination**: During actor-critic learning, hidden states and teammate latents jointly condition policy and value heads. Imagined rollouts sample plausible z_t^team trajectories to simulate partner variability.
- **Deployment**: At test time, z_t^team is inferred online from observed teammate actions — no access to the partner's policy, observations, or internal state is required.

## Datasets & evaluation
- **Proposed benchmarks** (no empirical results reported):
  - Multi-Agent Particle Environments (MPE) — diagnostic: whether the model learns identifiable teammate latents and separates social from physical dynamics.
  - Overcooked-AI — main testbed for zero-shot coordination with unseen partners using standard partner splits.
  - Melting Pot — robustness across diverse social contexts and partner populations at scale.
- **Proposed metrics**: Episodic return, sample efficiency, zero-shot coordination score, few-shot improvement rate, cross-play robustness.

## Limitations
- This is a proposal paper with no empirical validation — all claims are conceptual.
- Only considers two-agent cooperative settings; scaling to many agents is not addressed.
- The ToM head predicts only next-action distributions; richer teammate modeling (goals, beliefs, planning horizons) is not formalized.
- Evaluation protocol relies on existing benchmarks that may not fully stress the teammate factorization (e.g., MPE environments are relatively simple).

## Key takeaways
- The conceptual insight of factorizing world model latents into environment and social components is compelling — it reframes non-stationarity in MARL as a modeling problem rather than a learning problem.
- The proposal bridges two previously separate research threads: Dreamer-style world models and Theory-of-Mind agent modeling.
- If validated, this approach could enable world models to serve as "social simulators" — imagining not just physical dynamics but the behavior of other agents, supporting human-AI coordination without centralized training.
