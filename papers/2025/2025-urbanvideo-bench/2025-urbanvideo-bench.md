---
title: "UrbanVideo-Bench: Benchmarking Vision-Language Models on Embodied Intelligence with Video Data in Urban Spaces"
authors: [Jianjie Fang, Zichao Dai, Ziyou Wang, Jirong Zha, Weichen Zhang, Chen Gao, Yue Wang, Jinqiang Cui, Xinlei Chen, Yong Li]
year: 2025
venue: "ACL 2025"
tags: [video-understanding, embodied-ai, spatial-reasoning, remote-sensing]
url: ""
pdf: "[[2025-urbanvideo-bench.pdf]]"
date_ingested: 2026-06-18
---

# UrbanVideo-Bench: Benchmarking Vision-Language Models on Embodied Intelligence with Video Data in Urban Spaces

![[2025-urbanvideo-bench-thumbnail.png]]

## Research gap

UrbanVideo-Bench introduces the first comprehensive benchmark for evaluating video-LLMs on embodied cognitive abilities in three-dimensional urban spaces. Using drone footage from real cities (Guangdong, China) and two simulated environments (EmbodiedCity and AerialVLN), the authors collected 1.5k video clips with 5.2k automatically generated multiple-choice questions. The benchmark tests 17 widely-used video-LLMs across diverse task types: scene perception (what is observed), goal detection (finding navigation targets), landmark localization, high-level planning, and three reasoning types (causal, counterfactual, associative). Results reveal significant limitations in current models' ability to understand spatial relationships, navigate complex urban environments, and perform counterfactual reasoning. Notably, causal reasoning correlates strongly with recall and navigation performance, while counterfactual reasoning shows weaker correlations, suggesting different cognitive mechanisms.

## Contributions

- First benchmark for evaluating embodied cognition in urban aerial spaces with first-person drone footage
- 1.5k video clips from real cities and simulated environments with 5.2k multiple-choice questions covering navigation and reasoning
- Comprehensive evaluation of 17 Video-LLMs revealing critical gaps in spatial reasoning and counterfactual understanding
- Analysis of task correlations showing causal reasoning as a key cognitive component
- Validation of Sim-to-Real transfer potential through fine-tuning experiments

## Method

The benchmark consists of three data sources: real urban footage from Guangdong Province, China (manually piloted drones), and two simulators (EmbodiedCity based on Beijing, AerialVLN based on virtual cities). For each video clip, the authors design questions across multiple task dimensions:

- **Recall/Perception**: Scene understanding and spatial awareness from aerial perspective
- **Planning**: High-level navigation decisions given goals
- **Reasoning**: Causal (why actions?), counterfactual (alternative outcomes?), and associative (spatial relationships?)

Questions are automatically generated based on video trajectories and annotations, ensuring consistency and reproducibility.

## Datasets & evaluation

Testing 17 video-LLMs shows:
- Significant performance gaps across all task types, indicating limitations in embodied understanding
- Causal reasoning exhibits strong positive correlation with recall, perception, and navigation tasks (ρ > 0.7)
- Counterfactual and associative reasoning show weaker correlations, suggesting these require different cognitive processes
- Sim-to-Real transfer is possible: fine-tuning on simulated data improves performance on real aerial footage

## Limitations

- How do models perform with longer video sequences and more complex multi-goal scenarios?
- Can models learn spatial mental models sufficient for planning in novel urban environments?
- What architectural improvements are needed for robust counterfactual reasoning in video?
- Generalization to other cities, weather conditions, and times of day remains to be tested

## Key takeaways

Builds on recent progress in video-LLMs and embodied AI while addressing gaps in existing benchmarks. Previous work focused on disembodied third-person video understanding or indoor/ground-level robotics. UrbanVideo-Bench extends embodied cognition evaluation to three-dimensional aerial navigation in complex, open-ended urban environments—a previously unexplored domain.
