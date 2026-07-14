---
title: "A Comprehensive Review of Multi-Agent Reinforcement Learning in Video Games"
authors: [Zhengyang Li, Qijin Ji, Xinghong Ling, Quan Liu]
year: 2025
venue: "IEEE Transactions on Games"
tags: [reinforcement-learning, multi-agent]
url: "https://arxiv.org/abs/2509.03682"
date_ingested: 2026-07-03
---

# A Comprehensive Review of Multi-Agent Reinforcement Learning in Video Games

![[2025-comprehensive-review-marl-video-games-thumbnail.png]]

## Research gap
While individual breakthroughs in multi-agent reinforcement learning (MARL) have been extensively documented — AlphaStar, OpenAI Five, GT Sophy — no prior survey comprehensively covers MARL across the full spectrum of video game genres, from turn-based two-agent games to real-time multi-agent titles. Existing reviews focus primarily on single-agent RL or on MARL theory without grounding in specific game implementations. A systematic review connecting MARL challenges, algorithms, and game-specific implementations was missing.

## Contributions
- A comprehensive survey of MARL applications across video game genres: turn-based games (Backgammon, Go), sports games (Google Research Football, Rocket League, Roller Champions), FPS games (Doom/ViZDoom, Minecraft, Quake III Arena), RTS games (StarCraft II), and MOBA games (Dota 2, Honor of Kings).
- Systematic analysis of five key MARL challenges in games: nonstationarity, partial observability, sparse rewards, team coordination/communication, and scalability.
- A novel method to estimate game complexity across five dimensions: Observability, State Space, Action Space, Reward Sparsity, and Multi-Agent Scale.
- Identification of dominant training paradigms (CTDE as the prevailing approach) and their suitability across different game types.
- Proposed future research directions for advancing MARL in game development.

## Method
This is a survey paper. The methodology involves:

1. **Corpus construction**: 84 reports collected through structured search across IEEE Xplore, ACM Digital Library, SpringerLink, Elsevier, Wiley, arXiv, and Google Scholar. 40 core studies used for synthesis, 44 supplementary works for background.

2. **Taxonomy by game genre**: Papers are organized by game type — turn-based two-agent games, competitive/sports games, FPS, RTS, and MOBA — enabling comparison of how MARL techniques adapt to different game mechanics.

3. **Challenge-driven analysis**: Five cross-cutting challenges are analyzed across all games:
   - **Nonstationarity**: Multiple agents violate MDP stationarity assumptions
   - **Partial observability**: Fog of war, limited camera views require POMDP formulations
   - **Sparse rewards**: Goal-scoring events are rare; reward shaping (e.g., Lucy-SKG's KRC) densifies signals
   - **Team coordination**: Credit assignment, communication protocols, role switching
   - **Scalability**: Growing agent populations increase joint action space exponentially

4. **Training paradigm classification**: Games are categorized by CTCE (centralized training/execution), CTDE (centralized training, decentralized execution — dominant), and DTDE (fully decentralized).

5. **Complexity estimation**: A proposed five-dimensional framework rates games on observability, state space, action space, reward sparsity, and multi-agent scale to enable cross-game comparison.

## Datasets & evaluation
No new datasets or experiments are introduced. The review synthesizes results from 40 core studies across 12+ game environments, comparing reported performance metrics (win rates, Elo ratings, human-level benchmarks) and training requirements (compute, steps, data). Key benchmarks discussed include AlphaStar's Grandmaster-level StarCraft II play, OpenAI Five's defeat of world champion Dota 2 team OG, GT Sophy's superhuman Gran Turismo performance, and Lucy-SKG's state-of-the-art Rocket League play.

## Limitations
- The review corpus is limited to 40 core studies; some relevant work may be missed due to non-standard terminology or limited indexing.
- Citation chaining and manual filtering introduce potential selection bias toward more visible or recent studies.
- The proposed game complexity estimation method is qualitative and not empirically validated.
- Coverage is weighted toward competitive games; cooperative-only and mixed-motive settings receive less attention.
- Rapidly evolving field means the survey may not capture the most recent developments.

## Key takeaways
- CTDE (Centralized Training, Decentralized Execution) has emerged as the dominant paradigm for MARL in games, balancing coordination during training with practical deployability.
- Self-play remains the most successful training technique for competitive MARL, but its effectiveness depends heavily on game-specific curriculum design and opponent diversity.
- Reward shaping is critical across all game types — from potential-based shaping in Rocket League to hierarchical rewards in football simulators — and multiplicative combinations (KRC) outperform linear ones in physics-rich environments.
- The gap between MARL in controlled game environments and real-world multi-agent systems remains significant, particularly regarding nonstationarity and scalability.
- Video games serve as uniquely valuable MARL testbeds because they provide well-defined rules, measurable objectives, and controllable complexity — but transferring learned coordination strategies to open-ended real-world tasks is largely unexplored.
