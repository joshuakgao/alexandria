---
title: "Theory of Space: Can Foundation Models Construct Spatial Beliefs through Active Exploration?"
authors: [Pingyue Zhang, Zihan Huang, Yue Wang, Jieyu Zhang, Letian Xue, Zihan Wang, Qineng Wang, Keshigeyan Chandrasegaran, Ruohan Zhang, Yejin Choi, Ranjay Krishna, Jiajun Wu, Li Fei-Fei, Manling Li]
year: 2026
venue: "ICLR 2026"
tags: [spatial-reasoning, embodied-ai, vision-language-models, simulation-environments]
url: "https://openreview.net/forum?id=8iPwqr6Adk"
pdf: "[[2026-theory-of-space.pdf]]"
date_ingested: 2026-06-19
---

# Theory of Space: Can Foundation Models Construct Spatial Beliefs through Active Exploration?

![[2026-theory-of-space-thumbnail.png]]

## Research gap
Existing benchmarks for spatial intelligence in foundation models are either passive (reasoning over given observations) or task-driven (achieving a specific goal). None evaluate whether agents can actively construct, revise, and maintain coherent internal spatial beliefs through self-directed exploration under partial observability — the core competency required for spatial embodied intelligence.

## Contributions
- Introduces **Theory of Space**, a conceptual framework (analogous to Theory of Mind) defining an agent's ability to construct, revise, and exploit internal spatial beliefs through active exploration.
- Proposes a benchmark with parallel text and vision environments built on ThreeDWorld and Objaverse, featuring procedurally generated multi-room layouts.
- Introduces **spatial belief probing** — prompting agents to externalize their cognitive map at each step, enabling direct measurement of internal spatial representations rather than only behavioral outcomes.
- Designs a **false belief paradigm** for spatial cognition, revealing belief inertia where agents fail to overwrite obsolete spatial priors.
- Provides scripted proxy agents (Scout, Strategist) to disentangle exploration quality from spatial reasoning ability.

## Method
Agents are placed in procedurally generated N×M grid environments with multiple rooms and objects. The action space includes Goto, Rotate (90°/180°/270°), Observe (90° FOV), and Query (get absolute coordinates). Both text-based (symbolic spatial descriptions) and vision-based (egocentric RGB from ThreeDWorld) environments are provided in parallel. Evaluation has two phases: (1) an exploration phase where agents autonomously gather information, and (2) a reasoning phase with downstream spatial tasks spanning route-level knowledge (pairwise direction, perspective-taking, action-to-view) and survey-level knowledge (allocentric mapping, mental rotation, localization). Cognitive map probing requires the agent to output its spatial belief as 2D coordinates at each step.

## Datasets & evaluation
- 100 procedurally generated scenes with 3 connected 6×6 rooms, 4 objects each (12 total), 384×384 images in vision mode.
- 2,700 questions per setting (3 questions × 9 tasks × 100 scenes).
- Models evaluated: GPT-5.2, Gemini-3 Pro, Claude-4.5 Sonnet, GLM-4.6V, Qwen3-VL (235B), InternVL-3.5 (241B).
- Key findings:
  - **Active-Passive Gap**: All models degrade when exploring actively vs. passively (GPT-5.2: 57.1→46.0; Gemini-3 Pro: 60.5→57.3).
  - **Exploration inefficiency**: Rule-based proxies reach target coverage in ~9 steps; foundation models need ≥14 steps with no accuracy improvement.
  - **Belief instability**: Perception is an initial bottleneck, but global beliefs further degrade over time even with correct local observations.
  - **Belief inertia**: In the false belief paradigm, agents retain obsolete spatial priors after environment changes, especially severe in vision-based models.

## Limitations
- Environments are grid-based with discrete spatial relations, limiting ecological validity compared to continuous real-world spaces.
- Only single-agent exploration is evaluated; multi-agent coordination and belief sharing remain unexplored.
- The Query action (absolute coordinates) is rarely used by models, leaving that capability under-tested.
- Open-source models are evaluated at fixed temperature 0, while proprietary models use temperature 1, creating a minor evaluation asymmetry.

## Key takeaways
- Active spatial exploration remains a major unsolved challenge for foundation models — the gap between passive reasoning and active belief construction is substantial across all tested models.
- Cognitive map probing is a powerful diagnostic tool that reveals failures invisible to end-task metrics: models can answer spatial questions correctly while holding incoherent internal representations.
- Belief inertia — the inability to revise spatial priors — is a critical failure mode, especially for vision-based models, suggesting that current architectures lack robust mechanisms for spatial memory update.
- The Theory of Space framework provides a principled evaluation paradigm that could extend to multi-agent settings and real-world embodied AI.
