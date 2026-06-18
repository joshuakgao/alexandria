---
title: "ESPIRE: A Diagnostic Benchmark for Embodied Spatial Reasoning of Vision-Language Models"
authors: [Yanpeng Zhao, Wentao Ding, Hongtao Li, Baoxiong Jia, Zilong Zheng]
year: 2026
tags: [embodied-memory, benchmarking, spatial-reasoning, vision-language-models]
url: ""
pdf: "[[2026-espire.pdf]]"
date_ingested: 2026-06-18
---

# ESPIRE: A Diagnostic Benchmark for Embodied Spatial Reasoning of Vision-Language Models

![[2026-espire-thumbnail.png]]

## Research gap

ESPIRE is a diagnostic benchmark for evaluating the embodied spatial reasoning capabilities of vision-language models (VLMs) in physically-grounded simulated environments. Unlike existing spatial benchmarks that rely on discriminative VQA (multiple-choice questions with distractors), ESPIRE adopts a fully generative evaluation paradigm: VLMs must generate 3D positions and orientations to localize targets and execute pick-and-place actions in Isaac Sim, directly connecting spatial understanding to robotic manipulation.

## Contributions

- A fully generative evaluation paradigm that decomposes embodied spatial reasoning into localization (2D pointing) and execution (6-DoF pose prediction), replacing discriminative VQA
- Systematic task design across spatial aspects × reference frames × reference objects, yielding 148 spatial reasoning types with multiple granularity levels
- Physically-grounded evaluation in Isaac Sim where predicted poses are validated by a motion planner (cuRobo), ensuring physical feasibility
- Functional program representation for task instructions on 3D scene graphs, enabling scalable, reproducible task generation
- Diagnostic analysis of frontier VLMs revealing orientation reasoning as the primary bottleneck and a large gap between passive understanding and acting-oriented spatial reasoning

## Method

**Task Decomposition:** Each robotic task splits into:
- *Localization:* VLM generates 2D pixel coordinates pointing at the target object/slot; evaluated by accuracy (fraction within target segmentation mask)
- *Execution:* VLM generates a 6-DoF goal pose (position + orientation) in SE(3); evaluated by acceptance rate (fraction of physically achievable poses via cuRobo motion planner)

**Systematic Task Design:** Three key factors — spatial aspect S ∈ {attribute, relationship, distance, orientation}, reference frame F ∈ {relative, intrinsic, absolute}, reference object O ∈ {oriented, non-oriented} — define task specifications C = (S, F, O). Within each specification, tasks vary in granularity (coarse to fine-grained).

**Environment:** Isaac Sim with photorealistic rendering; tabletop scenes (pick tasks) and shelf scenes (place tasks) with varying clutter levels; 2,220 localization and 2,220 execution instances.

**Task Generation:** Instructions represented as 3-tuple T = (C, A, P) where C is the task specification, A is the action type, and P is functional programs executed on scene graph G to produce ground-truth answers. This enables automatic, scalable task creation.

## Datasets & evaluation

- **Localization:** Best model (RoboBrain2.0-7B) achieves ~58% average accuracy on pick, ~54% on place; distance is the weakest spatial aspect across all models
- **Execution:** Significant drop from localization performance; orientation reasoning is the primary bottleneck for all models
- **Difficulty scaling:** Performance generally degrades with increasing task difficulty (easy → medium → hard) for both localization and execution
- **Spatially-enhanced models** (RoboBrain2.0-7B, Gemini2.5-Pro) show more balanced performance across spatial aspects, suggesting task-specific fine-tuning helps
- **Tool-free evaluation** isolates intrinsic VLM spatial reasoning from external tool capabilities (depth estimators, point cloud processors)
- **Prerequisites for success:** Successful execution requires both accurate localization and correct orientation prediction; models that succeed use fewer attempts on average

## Limitations

- Simulation-only evaluation — sim-to-real transfer of findings is assumed but not validated
- Limited to pick-and-place tasks; more complex manipulation (pushing, pouring, assembly) not covered
- The 148 spatial reasoning types, while systematic, may not cover all real-world spatial language variations
- Execution evaluation depends on cuRobo motion planner quality — planner failures could be misattributed to VLM failures
- No temporal or dynamic reasoning — all scenes are static snapshots
- The gap between localization and execution performance suggests that VLMs may need fundamentally different training for acting vs. understanding

## Key takeaways

The benchmark is designed systematically along three axes: (1) spatial aspects — attributes, relationships, distances, and orientations; (2) reference frames — relative, intrinsic, and absolute; and (3) reference objects — oriented vs. non-oriented. These factors combine into 148 spatial reasoning types at varying granularities (e.g., "left" → "leftmost" → "second leftmost" → "at the 11 o'clock position"). Each task decomposes into localization (pointing at the target in 2D) and execution (predicting a 6-DoF goal pose in SE(3)), bridging passive spatial understanding with acting-oriented reasoning.

Tasks are specified as functional programs executed on 3D scene graph representations of environment states, enabling scalable ground-truth generation. Two scene types (tabletop for pick, shelf for place) with varying clutter levels provide broad coverage. Evaluation of frontier VLMs reveals that: models perform much better on localization than execution (indicating passive understanding outpaces acting ability), orientation reasoning is the weakest spatial aspect across all models, and distance reasoning also poses significant challenges — highlighting critical gaps for embodied deployment.

ESPIRE fills a gap between static VQA benchmarks (Blink, CV-Bench, VSI-Bench, SpatialVQA) that lack physical grounding and real-world evaluations that lack scalability. Compared to VSI-Bench (which evaluates spatial intelligence from video QA), ESPIRE requires acting, not just understanding. Compared to Open6DOR and EB-Manipulation (simulation-based but with simplified scenes), ESPIRE provides systematic spatial-centric design with high clutter. The functional program representation on scene graphs connects to embodied memory systems that use scene graphs for spatial reasoning (GraphPad, HOV-SG, MoMa-LLM). The finding that orientation is the primary bottleneck complements VSI-Bench's finding that spatial reasoning (not perception) is the main bottleneck for MLLMs.
