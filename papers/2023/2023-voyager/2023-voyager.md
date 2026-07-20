---
title: "Voyager: An Open-Ended Embodied Agent with Large Language Models"
authors: [Guanzhi Wang, Yuqi Xie, Yunfan Jiang, Ajay Mandlekar, Chaowei Xiao, Yuke Zhu, Linxi Fan, Anima Anandkumar]
year: 2023
venue: "TMLR 2024"
tags: [embodied-ai]
url: "https://arxiv.org/abs/2305.16291"
date_ingested: 2026-07-18
---

# Voyager: An Open-Ended Embodied Agent with Large Language Models

![[2023-voyager-thumbnail.png]]

## Research gap
Prior embodied agents in open-ended environments like Minecraft rely on reinforcement learning or imitation learning over primitive actions, which struggle with systematic exploration, interpretability, and generalization. Existing LLM-based agents (ReAct, Reflexion, AutoGPT) can generate action plans but are not lifelong learners — they cannot progressively acquire, accumulate, and transfer skills over extended time spans. No prior system combines open-ended curriculum generation, persistent skill memory, and iterative self-improvement for continual embodied learning.

## Contributions
- Introduces **Voyager**, the first LLM-powered embodied lifelong learning agent that continuously explores, acquires diverse skills, and makes novel discoveries without human intervention in Minecraft.
- Proposes an **automatic curriculum** driven by GPT-4 that generates progressively harder tasks based on the agent's current state and exploration progress, functioning as an in-context form of novelty search.
- Develops an **ever-growing skill library** of executable code indexed by description embeddings, enabling compositional skill reuse and alleviating catastrophic forgetting.
- Introduces an **iterative prompting mechanism** incorporating environment feedback, execution errors, and self-verification (GPT-4 as critic) for program refinement.
- Demonstrates the skill library is a transferable, plug-and-play asset that boosts other methods (e.g., AutoGPT) when provided as a component.

## Method
Voyager uses GPT-4 as a blackbox (no fine-tuning) with three key components:

1. **Automatic Curriculum**: GPT-4 proposes tasks based on the agent's inventory, equipment, nearby entities, biome, time, health/hunger, position, and previously completed/failed tasks. The overarching directive is "discover as many diverse things as possible." GPT-3.5 provides additional context via self-asked Q&A (for cost efficiency). Tasks unfold bottom-up, adapting to the agent's live situation.

2. **Skill Library**: Each successfully verified skill is stored as executable JavaScript code, indexed by the embedding of its GPT-3.5-generated description in a vector database. For new tasks, the top-5 relevant skills are retrieved using the embedding of a GPT-3.5-generated task plan combined with environment feedback. Complex skills compose simpler ones, compounding capabilities over time.

3. **Iterative Prompting Mechanism**: Code generation iterates through rounds incorporating (a) environment feedback (intermediate execution progress), (b) execution errors from the code interpreter, and (c) self-verification by a separate GPT-4 critic that checks task success and provides critique on failure. The loop repeats until verification passes or 4 rounds elapse.

The agent operates on high-level Mineflayer JavaScript APIs (not raw pixel input or low-level motor commands), using code as the action space for temporally extended, compositional actions.

## Datasets & evaluation
**Environment**: MineDojo (Minecraft 1.16.5) with Mineflayer APIs for motor control.

**Baselines**: ReAct, Reflexion, AutoGPT — all re-implemented for Minecraft since they were originally NLP-only methods.

**Key results**:
- **Exploration**: 63 unique items in 160 prompting iterations, 3.3x more than baselines.
- **Tech tree mastery**: Unlocks wooden tools 15.3x faster, stone 8.5x faster, iron 6.4x faster than baselines. Voyager is the only method to unlock diamond tools.
- **Map coverage**: Traverses 2.3x longer distances across diverse terrains.
- **Zero-shot generalization**: In a new world with cleared inventory, Voyager consistently solves all 4 unseen tasks (diamond pickaxe, golden sword, lava bucket, compass) within 50 iterations; no baseline solves any.
- **Ablations**: Self-verification is the most critical feedback type (-73% items without it). Automatic curriculum removal causes -93% drop. GPT-4 obtains 5.7x more items than GPT-3.5 for code generation.

## Limitations
- High cost due to GPT-4 API usage (15x more expensive than GPT-3.5).
- No visual perception — relies on text-based game state via Mineflayer APIs, not screen pixels. Cannot perceive spatial details of 3D structures.
- LLM hallucinations: the curriculum occasionally proposes nonexistent items (e.g., "copper sword"), and code generation may call absent APIs or use invalid game mechanics.
- Self-verification can fail (e.g., not recognizing spider string as evidence of killing a spider).
- Relies on high-level Mineflayer APIs rather than raw pixel input, making it not directly comparable to low-level control approaches (VPT, DreamerV3).

## Key takeaways
- Code as the action space enables temporally extended, compositional, and interpretable skills that naturally support lifelong learning — skills compound as complex programs compose simpler ones, and the skill library provides a form of persistent procedural memory that avoids catastrophic forgetting.
- An LLM-driven automatic curriculum that adapts to the agent's live state is far more effective than random or manually designed curricula for open-ended exploration — the bottom-up, curiosity-driven approach mirrors how human players naturally progress.
- The skill library is a general-purpose, transferable asset: providing Voyager's learned skills to AutoGPT enables it to solve tasks it otherwise cannot, demonstrating that accumulated procedural knowledge has value independent of the specific agent architecture.
- Self-verification (LLM as critic) is the single most important component — without reliable task completion assessment, the agent cannot know when to commit skills or move on, making all other components less effective.
