---
title: "Embodied LLM Agents Learn to Cooperate in Organized Teams"
authors: [Xudong Guo, Kaixuan Huang, Jiale Liu, Wenhui Fan, Natalia Vélez, Qingyun Wu, Huazheng Wang, Thomas L. Griffiths, Mengdi Wang]
year: 2024
tags: [multi-agent, theory-of-mind]
url: "https://arxiv.org/abs/2403.12482"
date_ingested: 2026-07-12
---

# Embodied LLM Agents Learn to Cooperate in Organized Teams

![[2024-embodied-llm-agents-organized-teams-thumbnail.png]]

## Research gap
LLM agents in multi-agent settings suffer from over-reporting, redundant communication, and instruction compliance without coordination — leading to chaotic interactions when three or more agents collaborate freely. Prior multi-LLM-agent work was limited to two-agent or fixed-structure settings and did not explore how organizational structures affect team efficiency or how to optimize them automatically.

## Contributions
- Introduces a multi-LLM-agent architecture for 3+ embodied agents with disentangled communication and action phases, enabling flexible organizational structures via prompting.
- Demonstrates that hierarchical organization with designated leadership improves team efficiency by up to 30% with minimal communication overhead (~3%), consistent with human organization theory.
- Develops a Criticize-Reflect framework where LLMs automatically generate improved organizational prompts, producing novel team structures that reduce communication cost and improve efficiency.
- Tests human-agent collaboration, finding that human leaders significantly outperform AI leaders at coordinating agent teams.

## Method
The architecture extends prior two-agent embodied LLM frameworks to 3+ agents with two key design choices: (1) separate Actor and Communicator LLMs disentangle action and communication decision-making, and (2) organizational structures are imposed via prompt-based role descriptions (e.g., leader, follower).

Each timestep alternates between a **communication phase** (agents take turns broadcasting, selecting recipients, or staying silent) and an **action phase** (agents execute environment actions). Organizational prompts specify communication protocols — who reports to whom, who delegates tasks, and how information flows.

The **Criticize-Reflect** framework uses a dual-LLM architecture: after each episode, a Critic analyzes team trajectories and performance, then a Coordinator proposes improved organizational prompts for the next episode. This iterative process discovers novel organization structures without human design.

## Datasets & evaluation
Experiments use the ThreeDWorld Transport Challenge and Communicative Watch-And-Help environments with 3 and 5 GPT-4 agents:
- **Hierarchical vs. flat**: Teams with a designated leader complete tasks up to 30% faster than unorganized teams, with negligible communication overhead.
- **Emergent behaviors**: Organized agents spontaneously exhibit task delegation, progress reporting, help-seeking, and constructive suggestions — mimicking human team dynamics.
- **Criticize-Reflect**: Automatically optimized organizational prompts produce novel structures (e.g., dynamic leadership rotation, specialized communication channels) that further improve efficiency and reduce communication costs.
- **Human-agent teams**: Human leaders coordinate agent teams far more effectively than LLM leaders, suggesting a capability gap in strategic task allocation.
- **Leadership election**: LLM agents can elect their own leader through communication, though the quality of emergent leadership varies.

## Limitations
- Only tested with GPT-4; generalization to other LLMs is uncertain.
- Environments (transport and household tasks) are relatively simple — scalability to complex, long-horizon tasks with many agents is untested.
- The Criticize-Reflect process requires multiple full episodes to converge, making it expensive for complex tasks.
- Communication is text-based; no grounding in visual or physical signals shared between agents.
- Organizational prompts are brittle — small wording changes can significantly affect team behavior.

## Key takeaways
- Unstructured communication among LLM agents leads to chaos (redundancy, interruption, conflicting instructions); organizational structure via prompting is necessary for effective coordination of 3+ agents.
- Hierarchical organization is a simple, effective intervention that mirrors findings from human organization theory — leadership reduces coordination overhead by channeling information flow.
- LLMs can serve as meta-optimizers of their own organizational structures through the Criticize-Reflect loop, discovering novel team configurations that outperform human-designed organizations.
- The gap between human and AI leadership in human-agent teams suggests that LLMs lack strategic planning and real-time adaptation skills critical for team coordination.
