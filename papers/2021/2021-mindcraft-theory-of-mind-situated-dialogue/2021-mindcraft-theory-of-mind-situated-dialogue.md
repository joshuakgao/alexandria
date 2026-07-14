---
title: "MindCraft: Theory of Mind Modeling for Situated Dialogue in Collaborative Tasks"
authors: [Cristian-Paul Bara, Sky CH-Wang, Joyce Chai]
year: 2021
venue: "EMNLP 2021"
tags: [theory-of-mind, multi-agent, human-ai-collaboration]
url: "https://arxiv.org/abs/2109.06275"
date_ingested: 2026-07-12
---

# MindCraft: Theory of Mind Modeling for Situated Dialogue in Collaborative Tasks

![[2021-mindcraft-theory-of-mind-situated-dialogue-thumbnail.png]]

## Research gap
Prior work on situated collaborative dialogue used Leader-Follower setups where one agent instructs and another executes, lacking the asymmetric knowledge and skill dynamics of real collaboration. No existing datasets for situated dialogue provided fine-grained, self-reported belief states that capture how partners' mental models of each other evolve during interaction — previous mental state annotations relied on external post-hoc attribution rather than in-situ self-reporting.

## Contributions
- Introduces MindCraft, a fine-grained dataset of 100 collaborative games in Minecraft's 3D blocks world where pairs of human players with asymmetric knowledge and skills collaborate toward joint goals.
- Captures self-reported belief states at periodic intervals during interaction, providing ground-truth theory-of-mind annotations (completed task status, partner knowledge, partner current task).
- Demonstrates that common ground between partners increases over the course of collaboration, with different belief types converging at different rates.
- Builds baseline computational models (LSTM and Transformer architectures) for predicting partner belief states from first-person video and dialogue history.

## Method
Pairs of players are placed in a Minecraft environment with a shared crafting goal. Each player receives a partial recipe (asymmetric knowledge) and a subset of tools (asymmetric skills), forcing communication and coordination. Every 75 seconds, players are prompted with paired questions probing three belief types: (1) whether a sub-task has been completed, (2) whether the partner knows how to make a specific material, and (3) what the partner is currently working on.

The computational model uses a sequence-to-sequence architecture processing two modalities: first-person video (encoded with a CNN) and dialogue (encoded with BERT). A GRU encodes the player's partial plan. These are fed through either an LSTM or masked Transformer to produce temporal representations, from which a feed-forward network predicts answers to belief questions.

## Datasets & evaluation
The dataset contains 100 games with 2,091 dialogue exchanges, averaging 7 minutes 23 seconds per game. Models are evaluated on three belief prediction tasks using weighted F1:
- **Completed task status**: Transformer with video-only input performs best, suggesting visual observation of the environment is most informative ("seeing is believing").
- **Partner knowledge**: All modality combinations perform similarly, as knowledge can be inferred from both dialogue and environmental observation.
- **Partner current task**: The hardest prediction — models fail to match the human trend of increasing agreement over time, indicating that inferring a partner's immediate goals requires deeper task understanding beyond perceptual signals.

## Limitations
- Small dataset (100 games, 12 hours of interaction) limits model generalization.
- Minecraft blocks world is a simplified proxy for real-world collaboration — asymmetric knowledge and skills are engineered rather than naturally occurring.
- Models are passive observers predicting belief states, not active agents that can ask clarifying questions or take actions to reduce uncertainty.
- The 21-class current-task prediction is particularly challenging, and models do not capture the temporal improvement seen in human performance.

## Key takeaways
- In situated collaboration, visual observation of a shared environment is as important as — or more important than — language for maintaining common ground about task completion status.
- Communication becomes critical for inferring partner mental states that are not directly observable (current goals, knowledge), highlighting the need for agents that can actively engage in dialogue.
- Asymmetry in knowledge and skills drives both the quantity of communication and the difficulty of maintaining mutual belief alignment — fully disparate settings produce the most dialogue and lowest agreement.
- Self-reported belief annotations provide richer ground truth for theory-of-mind modeling than post-hoc external annotation, capturing genuine uncertainty and partial knowledge.
