---
title: "Lucy-SKG: Learning to Play Rocket League Efficiently Using Deep Reinforcement Learning"
authors: [Vasileios Moschopoulos, Pantelis Kyriakidis, Aristotelis Lazaridis, Ioannis Vlahavas]
year: 2023
tags: [reinforcement-learning]
url: "https://arxiv.org/abs/2305.15801"
date_ingested: 2026-07-03
---

# Lucy-SKG: Learning to Play Rocket League Efficiently Using Deep Reinforcement Learning

![[2023-lucy-skg-rocket-league-reinforcement-learning-thumbnail.png]]

## Research gap
Rocket League is a complex 3D multiplayer physics-based game requiring aerial maneuvering, team coordination, and mastery of non-linear dynamics — yet it remained relatively under-explored by the RL community. Existing bots (Necto, Nexto) achieved strong play but relied on linear reward combinations and lacked systematic reward analysis or auxiliary learning techniques. No prior work provided a thorough investigation of sample-efficient deep RL techniques for this domain.

## Contributions
- A reward analysis and visualization library (`rlgym-reward-analysis`) for studying reward functions within Rocket League's arena geometry.
- Kinesthetic Reward Combination (KRC), a parameterizable alternative to linear reward combinations that better captures utility of complex physical phenomena (e.g., ball-to-goal alignment weighted by velocity and proximity).
- A simplified observation space with action stacking that doubles training FPS while maintaining competitive performance.
- On-policy auxiliary neural architectures for reward prediction and state representation tasks, improving sample efficiency.
- Lucy-SKG achieves state-of-the-art performance, defeating both Necto (2022 bot champion) and Nexto with significant margins (300-4 win ratio vs Necto at 1B steps).

## Method
Lucy-SKG builds on PPO with several enhancements:

1. **Kinesthetic Reward Combination (KRC)**: Instead of linearly combining reward components, KRC uses parameterized functions that capture the multiplicative interaction between physical quantities. For example, ball-to-goal alignment reward is weighted by ball velocity and proximity, creating a gradient that naturally guides the agent toward scoring behaviors. Potential-based reward shaping (Ng et al., 1999) is used to densify the sparse goal-scoring reward.

2. **Simplified observation space**: Reduces the state representation by removing redundant information and stacking the 5 most recent actions, which compensates for the reduced state information. This doubles training throughput (~4000 vs ~2000 FPS).

3. **Auxiliary tasks**: Two on-policy auxiliary objectives are trained alongside the main RL objective:
   - **Reward prediction (RP)**: Predicts the current reward from shared network representations, providing immediate gradient signals about environment dynamics.
   - **State representation (SR)**: Autoencoder-based reconstruction of environment state, regularizing internal representations.
   Both share early layers with the policy network and are weighted by task-specific coefficients in the total loss.

4. **Architecture**: Uses an attention-based shared architecture between main and auxiliary networks, with LSTM for temporal modeling.

## Datasets & evaluation
Evaluation is conducted in the RLGym framework (OpenAI Gym interface for Rocket League) in 1v1 matches. Lucy-SKG is compared against:
- **Necto**: 2022 Rocket League Bot Championship winner
- **Nexto**: Necto's successor, the highest-ranking bot at time of writing

At 1B training steps, Lucy-SKG achieves a 300-4 win record against Necto (99.77% win rate) and defeats Nexto 100% of the time. Ablation studies isolate the contribution of each component: KRC improves over linear reward combinations (Necto-Reward outperforms vanilla Necto), auxiliary tasks increase sample efficiency, and action stacking compensates for the simplified observation space.

## Limitations
- Training limited to 1B-2B steps due to computational constraints; agents do not yet challenge expert human players.
- Evaluated only in 1v1 matches; team-based play (2v2, 3v3) with its richer coordination requirements is not explored.
- The simplified observation space and auxiliary task architectures use relatively basic shared-layer designs; more sophisticated representation sharing could yield further gains.
- KRC reward functions require manual design and weight tuning specific to Rocket League's geometry.

## Key takeaways
- Reward shaping through multiplicative kinesthetic combinations (KRC) substantially outperforms linear reward weighting for physics-rich environments, because it captures the interaction between physical quantities rather than treating them independently.
- On-policy auxiliary tasks (especially reward prediction) provide significant sample efficiency gains in complex 3D environments, consistent with findings in simpler domains.
- A simplified observation space paired with action stacking can double training throughput with minimal performance loss — suggesting that many RL environments provide redundant state information.
- The work demonstrates that careful reward engineering combined with auxiliary learning can achieve state-of-the-art game-playing performance with relatively modest compute, opening pathways for sample-efficient RL in complex team-based physics games.
