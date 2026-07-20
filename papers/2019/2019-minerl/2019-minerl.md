---
title: "MineRL: A Large-Scale Dataset of Minecraft Demonstrations"
authors: [William Guss, Brandon Houghton, Nicholay Topin, Phillip Wang, Cayden Codel, Manuela Veloso, Ruslan Salakhutdinov]
year: 2019
venue: "IJCAI 2019"
tags: [embodied-ai]
url: "https://arxiv.org/abs/1907.13440"
date_ingested: 2026-07-17
---

# MineRL: A Large-Scale Dataset of Minecraft Demonstrations

![[2019-minerl-thumbnail.png]]

## Research gap
Standard deep reinforcement learning methods require enormous numbers of environment samples (hundreds of millions of frames or more), making them impractical for complex, open-world domains. Methods that leverage human demonstrations can improve sample efficiency, but the field lacked a large-scale, structured, simulator-paired dataset of human demonstrations for a domain with sufficient complexity, hierarchical task structure, and diversity.

## Contributions
- Introduced MineRL, a dataset of over 60 million automatically annotated state-action pairs of human demonstrations across six tasks in Minecraft.
- Developed a novel, extensible data collection platform that records packet-level information from a public Minecraft server, enabling perfect reconstruction and re-rendering of player demonstrations.
- Defined six benchmark tasks (Navigate, Treechop, ObtainIronPickaxe, ObtainDiamond, ObtainCookedMeat, ObtainBed) capturing hierarchical planning, navigation, and long-horizon challenges.
- Demonstrated that standard RL methods fail on these tasks while methods leveraging human demonstrations show meaningful improvement.

## Method
The data collection platform consists of three components: (1) a public Minecraft game server where consenting players complete stand-alone tasks for in-game currency; (2) a custom client plugin that records all packet-level client-server communication, allowing full re-simulation and re-rendering of demonstrations; and (3) a data processing pipeline that automatically annotates trajectories with game-state metadata (inventory, item events, player attributes, subtask completion markers). Each trajectory is sampled at 20 game ticks per second, with each state comprising an RGB video frame and comprehensive game-state features, and each action capturing keyboard inputs, mouse movement, GUI interactions, and crafting events.

## Datasets & evaluation
The MineRL-v0 dataset contains 500+ hours of human demonstrations across six tasks, rendered at two resolutions (64×64 and 192×256) with two texture sets. Baseline evaluations compared Random, DQN, A2C, Pretrained DQN (using demonstrations), and Behavioral Cloning on Treechop and Navigate tasks. All learned agents performed significantly worse than humans — on Treechop, humans scored 64 while the best RL agent scored under 4. Methods leveraging human demonstrations consistently outperformed pure RL methods, with Pretrained DQN achieving higher reward with fewer samples.

## Limitations
- The initial release covers only six tasks, a small subset of Minecraft's full complexity.
- Baseline methods still fall far short of human performance even with demonstrations, indicating the dataset alone does not solve the underlying algorithmic challenges.
- The action space was discretized into 10 primitives for RL baselines, losing the continuous nature of human mouse control.
- Data quality varies across demonstrations since players have different skill levels.

## Key takeaways
- Minecraft's hierarchical item dependencies and open-world nature create genuinely difficult RL benchmarks where standard methods produce near-zero reward on multi-step tasks like ObtainDiamond.
- Packet-level recording is an effective strategy for scalable, low-cost demonstration collection — the public server community provides ongoing data without per-demonstration cost.
- The dataset established the foundation for the MineRL competition series, catalyzing research on sample-efficient RL from human demonstrations in complex 3D environments.
