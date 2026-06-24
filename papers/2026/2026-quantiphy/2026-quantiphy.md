---
title: "QuantiPhy: A Quantitative Benchmark Evaluating Physical Reasoning Abilities of Vision-Language Models"
authors: [Puyin Li, Tiange Xiang, Ella Mao, Shirley Wei, Xinye Chen, Adnan Masood, Li Fei-Fei, Ehsan Adeli]
year: 2026
venue: "CVPR 2026"
tags: [vision-language-models, spatial-reasoning, physical-reasoning]
url: "https://arxiv.org/abs/2506.quantiphy"
pdf: "[[2026-quantiphy.pdf]]"
date_ingested: 2026-06-20
---

# QuantiPhy: A Quantitative Benchmark Evaluating Physical Reasoning Abilities of Vision-Language Models

![[2026-quantiphy-thumbnail.png]]

## Research gap
Existing benchmarks for VLM physical understanding are predominantly VQA-based and qualitative, using multiple-choice questions that cannot distinguish between near-correct and wildly wrong numerical estimates. No benchmark systematically evaluates whether VLMs can perform quantitative kinematic inference — estimating object size, velocity, and acceleration in real-world units from video observations.

## Contributions
- Propose a **quantitative evaluation paradigm** for VLM physical reasoning, moving beyond qualitative VQA to numerical accuracy measurement.
- Define a **kinematic inference task** for videos: given a single physical prior (size, velocity, or acceleration in real-world units), infer other kinematic properties of objects, categorized along dimensionality (2D/3D) and prior type (static/dynamic).
- Present **QuantiPhy**, the first benchmark for this task, comprising 3,391 video-text instances with numerical ground truth across 560 videos from Blender simulations, lab captures, and internet scraping.
- Evaluate 21 state-of-the-art VLMs (6 proprietary, 15 open-weight) plus a human baseline, revealing a consistent gap between qualitative plausibility and numerical correctness.
- Provide in-depth analysis showing VLMs rely on **pre-trained world knowledge** rather than faithfully using provided visual and textual inputs for quantitative reasoning.

## Method
- **Task formulation**: Given a video and a single physical prior for a source object (size, velocity, or acceleration in world units), the VLM must estimate requested kinematic properties of a target object. A pixel-to-world scale factor ω relates pixel-space and world-space quantities.
- **Four task categories**: 2D-Static, 2D-Dynamic, 3D-Static, 3D-Dynamic, defined by whether motion includes depth variation and whether the prior is a constant (size) or time-dependent (velocity/acceleration).
- **Data sources**: Blender simulations (controllable, precise ground truth), lab captures (multi-view stereo with depth cameras for 4D reconstruction), and internet scraping (monocular videos with manual annotation via reference objects).
- **Metric**: Mean Relative Accuracy (MRA) — averages accuracy across a spectrum of relative error tolerance thresholds (0.1 to 0.95), providing calibrated assessment of numerical proximity.
- **Analysis probes**: Prior-only evaluation (remove video, keep text), counterfactual analysis (scale priors by factors 0.001–700×), and chain-of-thought prompting to diagnose whether models truly use visual input and priors.

## Datasets & evaluation
- **QuantiPhy**: 560 videos, 3,391 questions across microscopic-to-astronomical scales. Videos are 2–3 seconds, ~120MB total.
- **Best proprietary model**: ChatGPT-5.1 at 53.1% MRA overall, slightly surpassing humans on 2D-Dynamic but below the human average of 55.6%.
- **Best open-weight model**: Qwen3-VL-Instruct-32B at 46.0% MRA.
- **Prior-only setting**: Models achieve scores close to the video+prior setting, indicating limited added value from actual video frames — models behave as "powerful guessers conditioned on textual hints."
- **Counterfactual analysis**: When priors are scaled by arbitrary factors, model predictions remain anchored to real-world magnitudes rather than tracking the altered priors — most models' MRA drops by ~80%, confirming they memorize rather than reason.
- **Chain-of-thought prompting**: Mostly unhelpful; only 3 of 21 models improve, as decomposition amplifies early numerical errors.

## Limitations
- Dataset covers only translational motion (no rotational dynamics).
- Fixed camera perspective; no moving viewpoints.
- Rigid objects only; no soft or deformable materials.
- Relatively simple isolated motions rather than complex multi-object interactions.
- Human baseline is not a theoretical ceiling — an ideal agent with precise pixel access could outperform humans significantly.

## Key takeaways
- State-of-the-art VLMs cannot reliably connect visual observations with quantitative physical facts; even the best models cluster around 50% MRA, comparable to or below human baselines that rely on coarse visual approximation.
- VLMs are not input-faithful quantitative reasoners: they weakly exploit pixel-level video information and do not reliably condition on provided numerical priors, instead relying on memorized world knowledge.
- Scaling model parameters improves performance (especially on dynamic categories) but does not close the gap to faithful physical reasoning.
- The benchmark exposes a fundamental capability gap: VLMs behave as approximate guessers rather than precise visual measurers, limiting their reliability for embodied AI applications.
