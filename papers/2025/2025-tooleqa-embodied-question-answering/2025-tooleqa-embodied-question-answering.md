---
title: "Multi-Step Reasoning for Embodied Question Answering via Tool Augmentation"
authors: [Mingliang Zhai, Hansheng Liang, Xiaomeng Fan, Zhi Gao, Chuanhao Li, Che Sun, Xu Bin, Yuwei Wu, Yunde Jia]
year: 2025
tags: [agentic-mllms, embodied-memory, vision-language-models]
url: ""
pdf: "[[2025-tooleqa-embodied-question-answering.pdf]]"
date_ingested: 2026-06-18
---

# Multi-Step Reasoning for Embodied Question Answering via Tool Augmentation

![[2025-tooleqa-embodied-question-answering-thumbnail.png]]

## Research gap

ToolEQA tackles embodied question answering (EQA)—where agents navigate 3D environments to answer questions—by integrating external tools with multi-step reasoning. Unlike prior VLM-based methods that directly explore without explicit planning, ToolEQA decomposes questions into structured plans, reasons about which tools to invoke, and uses tools (e.g., ObjectLocation3D for 3D object detection) to gather information. This approach generates more accurate responses with shorter exploration distances. The paper introduces a novel EQA data generation pipeline creating the EQA-RT dataset with 18K tasks including reasoning trajectories and answers. ToolEQA improves success rates by 9.2–20.2% over state-of-the-art baselines on EQA-RT-Seen and EQA-RT-Unseen benchmarks, and achieves state-of-the-art on HM-EQA, OpenEQA, and EXPRESS-Bench, demonstrating strong generalization across diverse EQA benchmarks.

## Contributions

- **Tool-augmented embodied reasoning**: First to systematically integrate external tools with multi-step reasoning for EQA
- **Structured planning**: Decomposes questions into explicit plans before exploration, reducing inefficient navigation
- **EQA-RT dataset**: Introduces 18K task dataset with reasoning trajectories enabling better training for tool-use agents
- **Tool specification design**: Carefully designed tool APIs (e.g., ObjectLocation3D) providing pixel-level 3D information
- **Generalization**: Strong cross-benchmark performance demonstrates approach generalizes beyond training data

## Method

**Agent architecture**:
- **Planner**: Decomposes question into step-by-step plan (e.g., "go to living room, find sofa, check size")
- **Reasoner**: At each step, decides which tool to invoke based on observations
- **Tool executor**: Invokes external tools providing precise information (3D object location, size, etc.)
- **Thought-action loop**: Iteratively reasons, acts, and observes until question is answered

**Key tools**:
- **ObjectLocation3D**: Detects object in image, returns 3D position and size
- **Spatial reasoning tools**: Compute relationships between objects
- **Arithmetic tools**: Compare measurements, calculate volumes

**Data generation pipeline**:
1. Sample question and environment from existing EQA benchmarks
2. Use oracle agent to generate optimal exploration trajectory
3. Extract reasoning steps and tool invocations from trajectory
4. Create EQA-RT dataset with diverse splits (seen/unseen environments)

**Training strategy**:
- Supervised learning on (question, plan, tool sequence, answer) tuples
- Multi-task training: predicting next plan step, tool selection, and final answer
- Enables generalization to new questions and environments

## Datasets & evaluation

- **EQA-RT-Seen**: 9.2% improvement in success rate over baselines
- **EQA-RT-Unseen**: 20.2% improvement on novel environments
- **Cross-benchmark**: State-of-the-art on HM-EQA, OpenEQA, EXPRESS-Bench
- **Exploration efficiency**: Shorter path lengths while achieving higher accuracy
- **Zero-shot baseline**: Even without dataset-specific training, ToolEQA achieves strong zero-shot performance

## Limitations

- How does performance scale to extremely large 3D environments?
- Can the approach handle real-world environments with imperfect tool reliability?
- How sensitive is planning to errors in early exploration steps?
- Can the system recover from tool failures or incorrect tool invocations?
- How does the approach generalize to multi-agent EQA scenarios?

## Key takeaways

ToolEQA builds on embodied reasoning work (NavigationEQA, etc.) but is novel in systematic integration of external tools. It relates to ReAct and other reasoning+action frameworks but applies them specifically to 3D navigation and perception. The tool design draws from point cloud understanding and 3D object detection literature.
