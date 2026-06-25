---
title: "SpinBench: Perspective and Rotation as a Lens on Spatial Reasoning in VLMs"
authors: [Yuyou Zhang, Radu Corcodel, Chiori Hori, Anoop Cherian, Ding Zhao]
year: 2025
venue: "ICLR 2026"
tags: [spatial-reasoning, vision-language-models, cognitive-ai]
url: "https://arxiv.org/abs/2509.25390"
pdf: "[[2025-spinbench.pdf]]"
date_ingested: 2026-06-24
---

# SpinBench: Perspective and Rotation as a Lens on Spatial Reasoning in VLMs

![[2025-spinbench-thumbnail.png]]

## Research gap
Despite rapid progress in vision-language models, their spatial reasoning capabilities remain poorly understood. Existing benchmarks either entangle spatial reasoning with high-level planning objectives or lack controlled variation in perspective, reference frame, and multi-frame reasoning. It remains unclear whether VLMs genuinely reason about space or rely on dataset biases and shallow pattern matching.

## Contributions
- A cognitively grounded diagnostic benchmark with 2,739 samples across 51 tasks organized into 7 progressively structured spatial reasoning categories, scaffolding from single-object perception to multi-object perspective taking.
- Controlled variation regime including allocentric/egocentric reference frame manipulation, premise-based question structures, and symmetrical and syntactic augmentations to probe reasoning consistency.
- Comprehensive evaluation of 43 state-of-the-art VLMs revealing systematic failure modes: egocentric bias, poor rotational understanding, and inconsistencies under equivalent reformulations.
- A human study showing 91.2% accuracy, with human response time strongly correlated with VLM accuracy, validating that SpinBench captures genuine spatial reasoning challenges.

## Method
SpinBench decomposes perspective taking into seven diagnostic categories of increasing complexity: (1) identity matching across viewpoints, (2) object-relation grounding in static scenes, (3) dynamic translation detection, (4) dynamic rotation detection, (5) canonical view selection, (6) mental rotation simulation, and (7) full perspective taking (scene selection and relation transformation). The benchmark combines synthetic data from Infinigen/Isaac Sim with real-world data from ABO household objects, multi-view car datasets, and stereo face databases. All tasks are constrained to horizontal 2D planes to minimize confounds. Controlled variations systematically manipulate reference frames, apply symmetrical and syntactic augmentations, and introduce premise-based variants to disentangle visual grounding failures from geometric reasoning failures.

## Datasets & evaluation
The benchmark uses four visual domains: Infinigen synthetic scenes (YCB objects), Amazon Berkeley Objects, Multi-View Car Dataset, and Stereo Face Database. Evaluation metrics include accuracy and Cohen's kappa (chance-corrected). GPT-5 ranks first in both overall accuracy and consistency. InternVL3-38B is the top open-source model (3rd overall accuracy, 2nd consistency). Most models perform at or below chance on mental rotation and perspective taking. Object-relation grounding is the easiest category (many models at kappa > 0.6). A strong correlation (r = 0.891) exists between overall accuracy and pairwise consistency.

## Limitations
- All tasks are restricted to horizontal 2D planes, excluding vertical spatial relations and height differences.
- Viewpoint changes are limited to horizontal orbits around scenes.
- The benchmark focuses on diagnostic value over task comprehensiveness, so it does not cover all aspects of spatial cognition.
- Chain-of-thought prompting shows limited and inconsistent benefits for spatial reasoning tasks.

## Key takeaways
- VLMs exhibit a persistent egocentric bias: 41% of models perform below chance on allocentric reference frame tasks, systematically misinterpreting the frame of reference.
- Rotation is particularly challenging: mental rotation and perspective taking yield near-chance scores for most models, revealing absent internal representations for rotational transformations.
- Consistency is a critical but insufficient indicator: while accuracy and consistency correlate strongly, top models can diverge substantially between the two, meaning high consistency does not guarantee reliable spatial reasoning.
- SpinBench shows weak correlation with existing spatial benchmarks, suggesting it captures novel and complementary diagnostic dimensions of VLM spatial competence.
