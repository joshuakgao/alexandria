---
title: "MineDojo: Building Open-Ended Embodied Agents with Internet-Scale Knowledge"
authors:
  - Linxi Fan
  - Guanzhi Wang
  - Yunfan Jiang
  - Ajay Mandlekar
  - Yuncong Yang
  - Haoyi Zhu
  - Andrew Tang
  - De-An Huang
  - Yuke Zhu
  - Anima Anandkumar
year: 2022
venue: NeurIPS 2022
tags:
  - embodied-ai
  - synthetic-datasets
  - reinforcement-learning
url: https://arxiv.org/abs/2206.08853
date_ingested: 2026-07-17
---

# MineDojo: Building Open-Ended Embodied Agents with Internet-Scale Knowledge

![[2022-minedojo-thumbnail.png]]

## Research gap
Prior embodied agents are trained tabula rasa in isolated environments with limited complexity and manually conceived objectives, making them narrow specialists that fail to generalize across diverse tasks. There is no unified framework that combines an open-ended environment, large-scale multimodal knowledge, and a scalable agent architecture for developing generalist embodied agents.

## Contributions
- A simulation platform built on Minecraft with thousands of diverse open-ended tasks specified in natural language, spanning programmatic tasks (survival, harvest, tech tree, combat) and creative tasks (building, exploration), two orders of magnitude larger than prior Minecraft benchmarks.
- An internet-scale multimodal knowledge base containing 730K+ YouTube videos with transcripts (33 years of footage), 6K+ Wiki pages, and 340K+ Reddit posts capturing the collective wisdom of millions of Minecraft players.
- MineCLIP, a contrastive video-language model pre-trained on YouTube data that serves as both a learned dense reward function for RL training and an automatic evaluation metric for open-ended tasks, eliminating the need for hand-engineered rewards.

## Method
MineCLIP uses a CLIP-style contrastive architecture with a text encoder and a factorized video encoder (per-frame image encoder + temporal aggregator). It is trained on 640K pairs of 16-second video snippets and time-aligned English transcripts from the YouTube database using InfoNCE loss. Two aggregator variants are explored: average pooling (fast, temporally agnostic) and transformer attention (slower, captures temporal ordering). During RL training, MineCLIP computes the correlation between a language goal and a 16-frame video snippet of agent behavior, providing a dense reward signal. The policy is trained with PPO and self-imitation learning. Efficiency optimizations include precomputing text features, reusing MineCLIP's image encoder as the agent's visual backbone, caching overlapping frame features, and storing high-reward trajectories for self-imitation.

## Datasets & evaluation
The benchmark suite includes 1,581 programmatic tasks with automated success criteria and 1,560 creative tasks evaluated via MineCLIP or human judgment. MineCLIP-guided agents achieve competitive performance with hand-engineered dense rewards on programmatic tasks (p=0.40, not statistically significant difference). On creative tasks, MineCLIP's binary success classification achieves 97–100% F1 agreement with human judges. A single 12-task multi-task agent shows both positive and negative transfer across task groups. Agents pre-trained on MineCLIP features demonstrate stronger zero-shot visual generalization to unseen terrains, weather, and lighting compared to vanilla CLIP baselines.

## Limitations
- The current agent is evaluated on only 12 of the thousands of available tasks; scaling to the full suite remains an open challenge.
- Creative task evaluation via MineCLIP is validated on only 4 tasks; broader coverage is needed.
- The Reddit and Wiki knowledge bases are not utilized in the current agent implementation.
- Negative transfer effects appear when training a single agent across all task groups simultaneously.
- The original OpenAI CLIP model completely fails in Minecraft due to the large visual domain gap, highlighting dependence on domain-specific fine-tuning.

## Key takeaways
- Internet-scale gameplay data from platforms like YouTube can replace expensive hand-engineered reward functions, enabling open-vocabulary, multi-task RL training for embodied agents.
- Contrastive video-language models pre-trained on in-the-wild videos transfer effectively to simulator environments despite significant domain gaps, and provide both reward signals and evaluation metrics.
- Minecraft's open-ended nature, massive player community, and procedural generation make it a uniquely suitable testbed for developing generalist embodied agents, far exceeding the task diversity of prior benchmarks.
