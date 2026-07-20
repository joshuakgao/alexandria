---
title: "MineLand: Simulating Large-Scale Multi-Agent Interactions with Limited Multimodal Senses and Physical Needs"
authors: [Xianhao Yu, Jiaqi Fu, Renjia Deng, Wenjuan Han]
year: 2024
tags: [multi-agent, embodied-ai]
url: "https://arxiv.org/abs/2403.19267"
date_ingested: 2026-07-19
---

# MineLand: Simulating Large-Scale Multi-Agent Interactions with Limited Multimodal Senses and Physical Needs

![[2024-mineland-thumbnail.png]]

## Research gap

Conventional multi-agent simulators assume perfect information and limitless capabilities, creating agents that diverge sharply from real-world human interaction. Existing Minecraft simulators (Malmo, MineDojo, MineRL) require a full game client per agent, making large-scale multi-agent simulation impractical on consumer hardware. No prior platform combined large agent counts with limited multimodal senses and physical needs to drive ecologically valid social interactions.

## Contributions

- **MineLand simulator**: A Minecraft-based multi-agent simulator supporting up to 48 agents on a single consumer desktop PC by replacing per-agent game clients with lightweight per-agent threads via an expanded Mineflayer architecture.
- **Limited multimodal senses**: Agents have restricted visual, auditory, and environmental awareness with distance attenuation, environmental obstructions, and directional constraints — forcing active communication to compensate for sensory deficiencies.
- **Physical needs**: Agents require food, sustenance, and resource management, adding time-based daily routine pressures that drive cooperation and competition for resources.
- **Benchmark suite**: 4,499 programmatic tasks, 1,536 creative tasks, and 18 hybrid tasks (construction and stage performance) with cooperative and competitive modes.
- **Alex agent framework**: A VLM-based agent inspired by multitasking theory from cognitive science, enabling simultaneous coordination and scheduling across multiple tasks with attention control and working memory.

## Method

**Simulator architecture**: Three modules — Bot (Python, provides environment info and agent APIs), Environment (Java, runs the Fabric server and executes actions), and Bridge (JavaScript, transfers information via Mineflayer). Each agent runs as a single thread rather than a full Minecraft client, enabling 48 concurrent agents.

**Observation space**: Multimodal senses including tactile (surrounding block information), auditory (distance-attenuated, obstruction-affected), and visual (first-person RGB with limited field of view). All information is raw perceptual data with realistic limitations.

**Action space**: Code-based actions via Mineflayer API. Agents generate executable JavaScript code for planning (moving, mining, crafting). A gate control system (New/Resume) allows agents to switch between or continue executing action sequences, with 50–200ms step granularity.

**Communication**: Distance-limited chat with interrupt capability — messages can interrupt ongoing code execution, enabling real-time multi-agent dialogue even during long-running tasks.

**Alex agent**: Components include memory, planning, acting, and a novel multitasking component inspired by Salvucci & Taatgen (2008). The multitasking component provides attention control (prioritizing among concurrent tasks) and working memory (saving/restoring internal states when switching between communication and goal-driven actions). Different personality traits are predefined in system prompts.

## Datasets & evaluation

**Programmatic tasks** (4,499): Clear success criteria with 5-tuple definition (goal, guidance, initial conditions, success criterion, parameters). Tasks span harvest, tech tree, and combat categories.

**Creative tasks** (1,536): No unique ground truth; evaluated qualitatively. Include exploration and open-ended building.

**Hybrid tasks** (18): Construction tasks scored via ORB feature matching between built structures and blueprints; stage performance tasks scored via longest common subsequence between agent action sequences and ground truth scripts.

**Key results**:
- MineLand supports 16 agents with visual display, 48 without — CPU/memory usage ~35% of Malmo for 8 agents.
- Vision-enhanced agents achieve 80% success rate (vs. 40% without vision) and 46s completion time (vs. 82s) on ocean-finding tasks.
- Multitasking agents handle 9/10 hurt events vs. 0/10 without multitasking.
- Limited senses force communication: agents actively seek information from peers when targets are outside their field of view.
- Agents with physical needs survive longer by prioritizing eating and shelter over exploration.
- Cooperative agents reduce per-agent workload by 20% (excluding communication cost).

## Limitations

- Maximum 48 agents, still far from simulating large-scale human societies.
- Reliance on commercial VLM APIs (GPT-4-vision-preview) for agent reasoning creates cost and latency constraints.
- Evaluation of creative tasks lacks rigorous quantitative metrics.
- The Alex agent achieves diamond collection only 2/6 times, indicating significant room for improvement on complex long-horizon tasks.
- Physical needs model is simplistic (health, food, oxygen) compared to real human needs hierarchies.
- All experiments use a single LLM/VLM configuration; robustness across different foundation models is untested.

## Key takeaways

- **Thread-per-agent architecture** is key to scaling: replacing full game clients with lightweight threads enables an order-of-magnitude increase in concurrent agents on consumer hardware.
- **Limited senses drive communication**: When agents cannot see or hear everything, they must actively seek information from peers, producing more ecologically valid social interactions than omniscient agents.
- **Physical needs create realistic prioritization**: Agents with hunger/health mechanics naturally develop survival-oriented behavior hierarchies (eat → shelter → explore), mirroring real-world Maslow-style prioritization.
- **Multitasking is essential for multi-agent settings**: Without attention control and interrupt handling, agents cannot respond to emergent events (attacks, peer communication) during long-running tasks.
- **Code-as-action avoids error accumulation**: Directly executing LLM-generated code via Mineflayer APIs is more reliable than the textual plan → controller translation pipeline used by prior work.
