---
title: "VipAct: Visual-Perception Enhancement via Specialized VLM Agent Collaboration and Tool-use"
authors: [Zhehao Zhang, Ryan Rossi, Sungchul Kim, Tong Yu, Franck Dernoncourt, Ruiyi Zhang, Jiuxiang Gu, Nedim Lipka]
year: 2025
venue: "AAAI 2025"
tags: [agentic-mllms]
url: ""
pdf: "[[2025-vipact-visual-perception-agent.pdf]]"
date_ingested: 2026-06-18
---

# VipAct: Visual-Perception Enhancement via Specialized VLM Agent Collaboration and Tool-use

![[2025-vipact-visual-perception-agent-thumbnail.png]]

## Research gap

VipAct is a multi-agent VLM framework addressing the challenge that state-of-the-art VLMs struggle with fine-grained visual perception tasks despite strong performance on coarse-level understanding. The framework consists of three core components: (1) an **orchestrator agent** managing task analysis, planning, and coordination, (2) **specialized agents** for tasks like image captioning and visual prompt description providing detailed analysis, and (3) **vision expert models** offering task-specific pixel-level information. Rather than attempting to perform all visual analysis in a monolithic VLM, VipAct decomposes tasks across multiple agents and experts, with orchestrator using System-2 reasoning to synthesize results. The approach achieves significant improvements on visual perception benchmarks featuring complex elements (visual prompts, multi-image inputs), while comprehensive ablations reveal critical roles of multi-agent collaboration and visual input for planning.

## Contributions

- **Multi-agent VLM framework**: First to systematically integrate orchestrator agent, specialized agents, and vision experts for fine-grained visual perception
- **Specialized agent design**: Separate agents for specific tasks (captioning, visual prompt description, comparison) enabling detailed analysis
- **Vision expert integration**: Leverages task-specific models (depth estimation, object detection, etc.) to overcome VLM pixel-level limitations
- **System-2 reasoning**: Orchestrator applies iterative reasoning and evidence aggregation to synthesize specialist outputs
- **Plug-and-play modularity**: Flexible framework allowing easy addition of new agents and experts

## Method

**Agent architecture**:

**Orchestrator agent**:
- Receives input image and query
- Performs task requirement analysis and planning
- Coordinates specialized agents and vision experts
- Synthesizes evidence and generates final answer

**Specialized agents** (examples):
- Image captioning agent: provides high-level scene description
- Visual prompt description agent: interprets visual artifacts (bounding boxes, masks)
- Image comparison agent: analyzes relationships between multiple images

**Vision expert models**:
- Depth estimation: pixel-level depth information
- Object detection: precise object localization and classification
- Visual prompt detector: locates and interprets visual annotations
- Similarity/segmentation models: task-specific perception

**Workflow**:
1. Orchestrator analyzes task requirements from image and query
2. Orchestrator plans which agents/experts to invoke
3. Specialized agents perform analysis, providing detailed descriptions
4. Vision experts provide pixel-level information
5. Orchestrator aggregates evidence and deduces final answer

**Key innovations**:
- **Evidence aggregation**: Combines outputs from multiple agents through natural language synthesis
- **Visual input for planning**: Uses actual image content (not just text query) to determine relevant agents
- **Error handling**: Identifies failed agents and leverages remaining evidence
- **Flexible agent selection**: Only invokes necessary agents per task

## Datasets & evaluation

- **Visual perception benchmarks**: Consistent improvements over state-of-the-art baselines
- **Ablation studies**: Demonstrates importance of:
  - Multi-agent collaboration for detailed System-2 reasoning
  - Visual input for task planning (vs. text-only planning)
  - Individual agents and experts
- **Error analysis**: Identifies VLM limitations in:
  - Fine-grained spatial understanding
  - Precise object localization
  - Boundary detection
  - Visual prompt interpretation

## Limitations

- How does performance scale with increasing number of agents and experts?
- Can agents learn to dynamically adjust their analysis depth based on task difficulty?
- How sensitive is performance to failures in individual agents or experts?
- Can the framework handle novel task types not seen during agent design?
- How much of the improvement comes from additional compute vs. better reasoning?

## Key takeaways

VipAct builds on VLM-based agent work (ViperGPT, VisProg, MM-REACT) but is novel in systematic multi-agent collaboration for perception. It addresses limitations of single-model approaches and rigid tool selection in prior visual programming methods. Relates to general LLM agent frameworks (ReAct, AutoGPT) but specializes for visual perception.
