---
title: "Mirage: The Illusion of Visual Understanding"
authors: [Mohammad Asadi, Jack W. O'Sullivan, Fang Cao, Tahoura Nedaee, Kamyar Rajabalifardi, Fei-Fei Li, Ehsan Adeli, Euan Ashley]
year: 2026
venue: ""
tags: [vision-language-models, medical-ai]
url: "https://arxiv.org/abs/2603.21687"
pdf: "[[2026-mirage-illusion-visual-understanding.pdf]]"
date_ingested: 2026-06-20
---

# Mirage: The Illusion of Visual Understanding

![[2026-mirage-illusion-visual-understanding-thumbnail.png]]

## Research gap
Multimodal AI systems report high scores on visual benchmarks, yet the degree to which they actually ground their reasoning in visual input is poorly understood. Prior work identified isolated benchmark flaws (language shortcuts, weak distractors, data contamination), but the full extent of non-visual inference — and its implications for high-stakes domains like medicine — had not been systematically characterized.

## Contributions
- Define the **mirage effect**: frontier VLMs confidently describe and reason about images that were never provided, constructing a false epistemic frame rather than merely hallucinating details within a valid one.
- Introduce **Phantom-0**, a benchmark measuring mirage rates across 20 categories; all tested frontier models (GPT-5.x, Gemini-3-Pro, Claude Opus 4.5) exhibit mirage behavior >60% of the time on average.
- Show that models in **mirage-mode** (no image input) retain 70–80% of their image-enabled benchmark accuracy across six widely used benchmarks, with medical benchmarks reaching up to 99% susceptibility.
- Train a 3B-parameter **text-only "super-guesser"** that outperforms all frontier VLMs and human radiologists on the held-out ReXVQA chest radiology benchmark — without seeing any images.
- Distinguish mirage-mode from explicit **guess-mode**: when models are told the image is missing, performance drops significantly, suggesting distinct internal reasoning regimes.
- Propose **B-Clean**, a post-hoc framework that filters benchmark questions answerable without visual input, enabling fairer vision-grounded evaluation.

## Method
- **Mirage rate measurement**: Visual questions are posed without accompanying images and without acknowledging the omission. Responses are classified as mirage (confident visual description) vs. normal (refusal or uncertainty).
- **Mirage-score**: For each model–benchmark pair, accuracy without images divided by accuracy with images, quantifying benchmark susceptibility to non-visual inference.
- **Super-guesser training**: Fine-tune Qwen-2.5 (3B, text-only) on the public ReXVQA training set with images removed. The base model predates the benchmark to minimize contamination risk.
- **Guess-mode comparison**: Same questions, but the prompt explicitly states the image is unavailable and asks the model to guess — revealing that mirage-mode engages a different, more confident reasoning pathway.
- **B-Clean framework**: Run mirage-mode evaluation per model to identify compromised questions; remove the union of all compromised sets, leaving only questions that no candidate model could answer without visual input.

## Datasets & evaluation
- **Phantom-0** (new): 20-category mirage-rate benchmark spanning medicine, science, technical, and general visual understanding.
- **General benchmarks**: MMMU-Pro, Video-MMMU, Video-MME.
- **Medical benchmarks**: VQA-Rad (radiology), MicroVQA (microscopy), MedXpertQA-MM (general medical QA), ReXVQA (chest radiology).
- Key results: mirage-mode retains 70–80% of full accuracy on average; medical benchmarks are most susceptible (up to 99%). The text-only super-guesser tops the ReXVQA leaderboard, surpassing radiologists by >10%. After B-Clean filtering, only 23–26% of benchmark questions survive.

## Limitations
- Mirage behavior is characterized on frontier models available at time of writing; newer architectures may behave differently.
- B-Clean requires running each candidate model in mirage-mode, adding computational cost proportional to the number of models compared.
- The super-guesser demonstration is limited to chest radiology; generalizability to other medical specialties or non-medical domains is not fully explored.
- The paper does not propose architectural interventions — B-Clean operates at the evaluation level only.

## Key takeaways
- Benchmark accuracy on multimodal tasks is a poor proxy for visual understanding; the non-visual component of performance consistently exceeds the visual component across all tested model–benchmark pairs.
- The mirage effect is distinct from hallucination: models construct entirely false epistemic frames (describing non-existent images) rather than embellishing real inputs.
- Medical AI benchmarks are especially vulnerable because medical questions are statistics-dominated and structurally patterned, enabling high accuracy from population-level priors alone.
- Public benchmarks are inherently ephemeral — they leak into pretraining data. Private, B-Clean-style evaluation is necessary for meaningful comparison of visual capabilities.
- The existence of two distinct reasoning regimes (mirage vs. guess) suggests that multimodal models do not simply pick the most probable answer — they engage qualitatively different processing depending on whether they "believe" an image is present.
