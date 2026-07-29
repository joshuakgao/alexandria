---
title: "TeamCraft: A Benchmark for Multi-Modal Multi-Agent Systems in Minecraft"
authors: [Qian Long, Zhi Li, Ran Gong, Ying Nian Wu, Demetri Terzopoulos, Xiaofeng Gao]
year: 2024
tags: [multi-agent, embodied-ai, vision-language-models]
url: "https://arxiv.org/abs/2412.05255"
date_ingested: 2026-07-19
---

# TeamCraft: A Benchmark for Multi-Modal Multi-Agent Systems in Minecraft

![[2024-teamcraft-thumbnail.png]]

## Research gap
Existing multi-agent benchmarks suffer from several limitations: many target only one or two agents, are confined to indoor settings with narrow task ranges, use purely state-based observations rather than visual input, and specify tasks via text alone — making it difficult to convey spatial information accurately. There was no comprehensive benchmark combining multi-modal task specification, visual observations, scalable agent counts, and systematic generalization evaluation for multi-agent collaboration.

## Contributions
- **TeamCraft benchmark**: A multi-modal multi-agent benchmark built on Minecraft featuring 55,000+ task variants specified by multi-modal prompts (interleaved text and orthographic projection images), with procedurally generated expert demonstrations for imitation learning.
- **Systematic generalization evaluation**: Carefully designed protocols to evaluate model generalization across novel goal configurations, unseen numbers of agents, novel agent capabilities, and new visual backgrounds.
- **Centralized and decentralized control**: Support for both centralized (full observability across agents) and decentralized (each agent sees only its own observations) control paradigms.
- **Extensive baseline analysis**: Evaluation of state-of-the-art multi-modal multi-agent models revealing significant challenges in generalization, with open-sourced platform, training/evaluation code, model checkpoints, and data.

## Method
TeamCraft uses Minecraft as its simulation environment, with a Gym-like interface built on Mineflayer for agent control. The system architecture consists of three components: a Minecraft server, Mineflayer as the agent control interface, and a Gym-like environment providing RGB and inventory observations.

**Task design**: Four task types test different collaboration facets:
- **Building** — agents erect structures from orthographic view blueprints, requiring visual cognition, spatial reasoning, and coordination of action dependencies (e.g., supporting blocks before floating ones).
- **Clearing** — agents remove blocks using appropriate tools, requiring tool-assignment coordination to optimize efficiency.
- **Farming** — agents sow and harvest crops, requiring maturity assessment, dynamic subtask allocation, and redundancy avoidance.
- **Smelting** — agents gather materials and use furnaces to produce goal items, requiring resource coordination and efficient furnace sharing.

**Multi-modal prompts**: Task specifications combine language instructions with orthographic projection images (top, left, front views) that specify initial, intermediate, or goal states.

**Action space**: Eight high-level self-explanatory skills (e.g., `obtainBlock`, `farmWork`, `placeItem`) parameterized by agent name, item name, and 3D target position, translated to low-level controls via Mineflayer APIs.

**Diversity**: 30+ target objects, 10+ biome scenes, varied inventories with distractors, and random tool assignments ensure rich combinatorial variation.

## Datasets & evaluation
- **55,000+ task variants** with corresponding procedurally generated expert demonstrations for imitation learning.
- **Generalization protocols**: Novel goals (unseen goal configurations), novel scenes (unseen biomes/backgrounds), unseen agent counts, novel agent capabilities, and combined generalization.
- **Baseline models evaluated**: Centralized and decentralized variants tested with VLM-based architectures. Results show existing models face significant challenges in all generalization dimensions, with performance dropping substantially on out-of-distribution conditions.

## Limitations
- High-level action space abstracts away low-level motor control, which limits evaluation of fine-grained manipulation.
- Minecraft's block-based world, while visually rich, has simplified physics compared to real-world environments.
- Expert demonstrations are procedurally generated (optimal but potentially lacking human-like suboptimality and diversity).
- Current baseline evaluations focus on VLM-based approaches; integration with MARL methods is not explored.

## Key takeaways
- Multi-modal prompts (interleaved text and images) are essential for specifying spatial task configurations that language alone cannot convey — extending the VIMA-Bench paradigm to multi-agent settings.
- Current VLM-based multi-agent models struggle significantly with generalization, particularly to unseen numbers of agents and novel goal configurations, highlighting the gap between single-agent and multi-agent visual reasoning.
- The benchmark reveals that the combination of visual understanding, spatial reasoning, and multi-agent coordination creates challenges far beyond what each component presents individually.
- Centralized vs. decentralized control creates a meaningful performance gap, with decentralized agents facing additional challenges from partial observability of teammates' states.
