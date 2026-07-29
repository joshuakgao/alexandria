---
title: "PhysEditWorld: A Large-Scale Dataset Toward Physics-Editable World Models"
authors: [Bin Hu, Yanwen Ma, Jiehui Huang, Ziliang Zhang, Haoning Wu, Ruicheng Zhang, Yaokun Li, Zijun Wang, Yuechen Zhang, Chun-Mei Tseng, Hanhui Li, Shengju Qian, Jun Zhou, Kaipeng Zhang, Xiaodan Liang, Jiaya Jia, Xiu Li]
year: 2026
tags: [world-models, physical-reasoning]
url: "https://arxiv.org/abs/2606.26694"
date_ingested: 2026-07-29
---

# PhysEditWorld: A Large-Scale Dataset Toward Physics-Editable World Models

![[2026-physeditworld-thumbnail.png]]

## Research gap
Current game world models learn physical dynamics as implicit correlations from data rather than as controllable variables. They can imitate how a game usually evolves but cannot answer how the same scene should evolve if a physical rule (e.g., gravity) is edited. Existing datasets and benchmarks — whether from game/RL environments (ALE, Procgen, MineRL), world-exploration datasets (Sekai), or physics reasoning benchmarks (PHYRE, CLEVRER, Physion, VBench, PhysInOne) — do not provide matched gameplay clips in which scene, interaction trace, and camera policy are held fixed while a physical parameter is explicitly changed.

## Contributions
- **PhysEditWorld dataset**: A large-scale multimodal dataset for physics-editable game world modeling, containing 12 cinematic UE5 scenes, 100+ hours of gameplay, and 60M+ rendered rollout frames organized around matched replay groups with explicit gravity interventions.
- **UE5 replay-and-rendering pipeline**: An in-editor plug-in that automatically replays the same scene, initial state, character controller, action trace, and camera policy under controlled gravity configurations, enabling attributable physical variation.
- **Multimodal annotations**: Synchronized RGB, depth, surface normals, audio, action traces, camera trajectories, engine states, semantic captions, and explicit gravity labels per frame.
- **Utility studies**: Demonstrations that PhysEditWorld enables evaluation and improvement of gravity awareness in generative video models, game world models, and video-language models.

## Method
PhysEditWorld is built on a **replay paradigm**: each scenario records a normalized action trace (via UE5's Enhanced Input System) and replays it under multiple gravity configurations (0.25g, 1g, 2g, 5g, etc.) while keeping everything else fixed.

**Pipeline stages:**
1. **Scenario and input construction** — artist-ready UE5 levels are converted into replayable scenarios via an in-editor plug-in. Action sequences are recorded as semantic Input Action sequences (movement axes, jump commands, camera deltas) rather than raw device events, ensuring hardware-agnostic reproducibility.
2. **Controlled UE5 simulation** — the DataFactory plug-in injects normalized action sequences back into the UE5 gameplay stack while varying only gravity. Scene, controller logic, input sequence, and camera policy remain fixed.
3. **Synchronized rendering** — 8 camera views (first-person, third-person, front, back, left, right, front-left, front-right) render RGB, depth, normal maps, and spatial audio at 30 FPS / 1280×720. Engine states (camera trajectories, character states, object states, physical variables) are logged in UE5's native coordinate system.
4. **Post-hoc annotation** — VLM-based semantic captioning describes the scenario and gravity-dependent motion differences.

The basic unit is a **matched replay group**: all non-gravity factors are controlled so that differences in jump height, airtime, fall speed, landing timing, and object trajectories are directly attributable to the gravity intervention.

## Datasets & evaluation
**Dataset scale**: 12 cinematic UE5 scenes, 100+ hours of gameplay, 60M+ rendered frames across multiple gravity levels and 8 camera views.

**Utility studies:**
- **Gravity-conditioned video generation** — fine-tuning generative video models on PhysEditWorld improves gravity-faithful dynamics modeling, though models still often under-express gravity-sensitive motion or confuse gravity-level ordering.
- **First-person world model rollouts** — world models trained with explicit gravity conditioning produce more physically consistent rollouts under edited gravity compared to baselines.
- **Video-language gravity inference** — VLMs tested on gravity-level classification from video clips show that current models can maintain visual realism but struggle with fine-grained physical discrimination between gravity levels.

## Limitations
- Current release focuses exclusively on gravity as the editable physical parameter; friction, bounciness, wind, and other physical variables are not yet included.
- Scenarios are limited to 12 UE5 scenes; diversity of game genres and interaction types could be expanded.
- The replay paradigm assumes deterministic engine behavior — stochastic physics effects or non-deterministic game logic may limit exact reproducibility.
- Evaluation is restricted to initial utility studies; comprehensive benchmarking of state-of-the-art world models across all gravity conditions is not yet provided.

## Key takeaways
- **Physics as editable design variables**: Game world modeling requires physical laws to be treated as explicit, controllable parameters rather than implicit data regularities — a capability absent from current world models.
- **Matched replay enables causal attribution**: By fixing scene, action, controller, and camera while varying only gravity, PhysEditWorld enables direct measurement of whether a model faithfully responds to physical edits — a stronger evaluation than visual plausibility alone.
- **Current models lack gravity sensitivity**: Across generative video models, world models, and VLMs, experiments reveal that models can maintain visual realism but systematically fail to accurately express gravity-dependent dynamics, suggesting editable physics remains a missing capability.
- **Scalable pipeline for physics-editable data**: The UE5 replay-and-rendering pipeline operates within standard game development workflows, providing a template for future datasets covering additional physical parameters.
