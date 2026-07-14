---
title: "SIMA 2: A Generalist Embodied Agent for Virtual Worlds"
authors: [SIMA Team, Google DeepMind]
year: 2025
tags: [embodied-ai, reinforcement-learning, world-models]
url: "https://arxiv.org/abs/2512.04797"
date_ingested: 2026-07-04
---

# SIMA 2: A Generalist Embodied Agent for Virtual Worlds

![[2025-sima-2-generalist-embodied-agent-thumbnail.png]]

## Research gap
SIMA 1 could follow short, direct language instructions across multiple 3D game environments but was limited to simple commands, could not engage in dialogue or reasoning, and showed brittleness when generalizing to new situations. More broadly, foundation models excel at passive understanding but struggle with active, goal-directed embodied interaction — a modern form of Moravec's Paradox.

## Contributions
- A Gemini-based embodied agent (VLA) that reasons, acts, and engages in dialogue across diverse 3D virtual worlds via keyboard-and-mouse actions.
- New capabilities over SIMA 1: embodied dialogue, internal reasoning, complex multi-step instruction following, and multi-modal prompting (e.g., sketches).
- Roughly doubles SIMA 1's success rate on embodied tasks, substantially closing the gap with human performance.
- Robust generalization to entirely held-out environments (ASKA, Minecraft/MineDojo) and photorealistic Genie 3 worlds.
- Open-ended self-improvement: the agent autonomously learns new skills from scratch in novel environments using foundation-model-generated tasks and rewards.

## Method
- **Architecture**: Gemini Flash-Lite model fine-tuned via SFT on a mixture of gameplay and Gemini pretraining data, preserving the base model's vision, language, reasoning, and dialogue capabilities.
- **Interface**: The agent perceives 720p RGB video frames and outputs structured text that is deterministically parsed into keyboard-and-mouse actions, plus natural language for dialogue/reasoning.
- **Human data**: Large-scale multi-modal dataset from human gameplay — single-person post-hoc annotated demonstrations and two-person "setter-solver" interactive annotation for causal instruction-action alignment.
- **Bridge data**: A smaller set of high-quality examples annotated by Gemini Pro with interleaved reasoning and dialogue, bridging embodied actions with language modalities.
- **Reinforcement learning**: Online RL from verifiable rewards on curated tasks (embodied task completion and environment-grounded QA) in training environments.
- **Self-improvement**: Three foundation models (task setter, agent, reward model) plus a world model (Genie 3) enable open-ended autonomous skill acquisition in novel environments.

## Datasets & evaluation
- **Training environments**: Construction Lab, Playhouse, WorldLab, Goat Simulator 3, Hydroneer, No Man's Sky, Satisfactory, Space Engineers, Valheim, Wobbly Life.
- **Held-out environments**: ASKA, Minecraft (MineDojo, 50 programmatic tasks), The Gunk, Genie 3 photorealistic worlds.
- **Evaluation types**: Ground-truth state evaluation (research envs), programmatic OCR/pixel-based evaluation (commercial games), and human evaluation (5 independent ratings per trial).
- **Key results**: SIMA 2 doubles SIMA 1's average success rate across both human-evaluated (~66% → ~86%) and automatically-evaluated (~33% → ~76%) tasks, approaching human-level performance. Strong generalization to held-out environments despite no environment-specific training.

## Limitations
- Performance gaps remain in resource gathering and combat skill categories compared to humans.
- Self-improvement demonstrated only in virtual environments; transfer to physical-world robotics is future work.
- Relies on Gemini's base capabilities — inherits its limitations in visual understanding and reasoning.
- Evaluation in held-out environments uses participants without task-specific training, making human baselines approximate.
- The agent perceives only RGB frames with no privileged environment state, limiting performance on tasks requiring precise spatial reasoning.

## Key takeaways
- Integrating a foundation model (Gemini) as the core of an embodied agent is a viable path to generalist agents that reason, act, and communicate in 3D worlds.
- The combination of human demonstration data, synthetic bridge data for reasoning/dialogue, and online RL produces a step-change in embodied performance over prior instruction-following agents.
- Open-ended self-improvement using foundation models for task generation and reward scoring enables autonomous skill acquisition in novel environments — pointing toward continuously learning agents.
- Generalization to photorealistic Genie 3 worlds suggests a virtuous cycle between increasingly capable world models and agents.
