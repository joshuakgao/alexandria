---
title: "Gymnasium: A Standard Interface for Reinforcement Learning Environments"
authors: [Mark Towers, Ariel Kwiatkowski, Jordan Terry, John U. Balis, Gianluca De Cola, Tristan Deleu, Manuel Goulão, Andreas Kallinteris, Markus Krimmel, Arjun KG, Rodrigo Perez-Vicente, Andrea Pierré, Sander Schulhoff, Jun Jet Tai, Hannah Tan, Omar G. Younis]
year: 2025
venue: "NeurIPS 2025"
tags: [simulation-environments, reinforcement-learning]
url: "https://arxiv.org/abs/2407.17032"
pdf: "[[2025-gymnasium.pdf]]"
date_ingested: 2026-06-18
---

# Gymnasium: A Standard Interface for Reinforcement Learning Environments

![[2025-gymnasium-thumbnail.png]]

## Research gap
OpenAI Gym had become the de facto standard API for RL environments but was abandoned in 2021 with no updates since October 2022. It suffered from a conflation of termination and truncation signals (causing subtle training bugs), limited vectorization support, and no functional environment API for theoretical/search-based research or hardware-accelerated environments.

## Contributions
- **Maintained successor to OpenAI Gym**: Over 18 million installations, 500+ GitHub issues, 800+ PRs from 40+ contributors. Full backward compatibility with Gym environments via conversion wrappers.
- **Termination vs. truncation distinction**: Separates episode-ending signals into two booleans — termination (state-dependent, task success/failure) vs. truncation (time-based cutoff). This distinction is critical for correct value estimation in algorithms like PPO, DQN, and SAC, and was previously conflated in Gym's single `done` boolean.
- **Functional environment API (FuncEnv)**: Maps directly to POMDP formalism (initial, transition, observation, reward, terminal functions). Enables theoretical RL research, search algorithms, and JAX-based hardware acceleration of environments.
- **First-class vectorized environments**: Three autoreset modes (next-step, same-step, disabled), sync/async/custom vectorization, and specialized vector wrappers. Custom vectorization enables order-of-magnitude speedups for simple environments via tensor operations.
- **Expanded algebraic spaces**: Support for text, graphs, disjoint unions, and variable-length sequences, enabling multi-modal observation/action environments.

## Method
**Core API (Env):**
- Corresponds to a POMDP. Defined by `observation_space`, `action_space`, `reset()`, and `step()` methods.
- `step()` returns `(observation, reward, terminated, truncated, info)` — the two-boolean design forces correct handling of episode boundaries.
- Registry system with namespace/name/version for reproducibility; version incremented on any behavioral change.
- 33 built-in wrappers (reward clipping, observation normalization, video recording, etc.).

**Functional API (FuncEnv):**
- Stateless functions: `initial(rng) → state`, `transition(state, action, rng) → state`, `observation(state) → obs`, `reward(state, action, next_state) → float`, `terminal(state) → bool`.
- Enables direct state access for search/planning algorithms, distribution collection over transitions, and JAX vectorization via `FunctionalJaxVectorEnv`.

**Vectorization (VectorEnv):**
- `SyncVectorEnv`: iterative computation, simple but sequential.
- `AsyncVectorEnv`: subprocess per environment, benefits on high-RAM systems but overhead dominates on weak hardware.
- Custom vectorization: environment-specific tensor-parallel implementations (e.g., CartPole computed entirely in parallel tensor ops).
- Three autoreset modes: next-step (default, fastest, dead actions on reset steps), same-step (Gym default, final obs in info), disabled (explicit reset mask).

**Built-in environments:**
- Classic control (CartPole, Acrobot, MountainCar, Pendulum), toy text (Blackjack, Taxi, FrozenLake), Box2D (BipedalWalker, CarRacing, LunarLander), MuJoCo (11 continuous control tasks).
- Extensive ecosystem: ALE/Atari, MetaWorld, Gymnasium Robotics, MiniGrid, Highway-env, and many third-party environments.

## Datasets & evaluation
- **Adoption**: 18M+ downloads since November 2023. Native support across major RL libraries: Stable Baselines3, CleanRL, Tianshou, Ray RLlib, Dopamine.
- **Vectorization benchmarks**: Custom vectorized CartPole achieves order-of-magnitude speedup over sync/async. AsyncVectorEnv benefits depend heavily on hardware (significant gains on high-RAM M3 MacBook, degraded performance on Google Colab due to subprocess overhead).
- **Termination/truncation impact**: Prior to Gymnasium's fix, few training libraries correctly differentiated termination from truncation, leading to incorrect value estimates and subtly degraded training.

## Limitations
- FuncEnv requires reimplementation of existing Env-based environments — no automatic conversion.
- Custom vectorization requires environment-specific engineering; only feasible for simple dynamics.
- The API assumes single-agent settings; multi-agent RL requires PettingZoo (separate library).
- Built-in environments are relatively simple (classic control, toy text); complex environments are third-party and quality varies.
- No built-in support for real-time or asynchronous deployment (relevant for robotics and game agents like NitroGen).

## Key takeaways
- **API design has outsized impact on research correctness.** The termination/truncation conflation in OpenAI Gym caused widespread, subtle bugs in value estimation across major RL libraries. A simple API change (two booleans instead of one) forced correct handling and improved training across the ecosystem.
- **Gymnasium is foundational infrastructure.** It underpins essentially all modern RL environment interaction — from Atari benchmarks to robotics (MetaWorld, Gymnasium Robotics) to gaming agents (NitroGen's universal simulator wraps games with the Gymnasium API). Understanding it is prerequisite context for any RL-adjacent work.
- **Functional APIs bridge theory and practice.** FuncEnv's alignment with POMDP formalism enables research patterns (state distribution collection, search algorithms, JAX acceleration) that are awkward or impossible with object-oriented Env, showing that API design choices have downstream algorithmic implications.
