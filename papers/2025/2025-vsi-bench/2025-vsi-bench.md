---
title: "Thinking in Space: How Multimodal Large Language Models See, Remember, and Recall Spaces"
authors: [Jihan Yang, Shusheng Yang, Anjali Gupta, Rilyn Han, Li Fei-Fei]
year: 2025
venue: "CVPR"
tags: [spatial-reasoning, benchmarking, vision-language-models, video-understanding, cognitive-ai]
url: ""
pdf: "[[2025-vsi-bench.pdf]]"
date_ingested: 2026-06-18
---

# Thinking in Space: How Multimodal Large Language Models See, Remember, and Recall Spaces

![[2025-vsi-bench-thumbnail.png]]

## Research gap

This paper introduces VSI-Bench (Visual-Spatial Intelligence Benchmark), a video-based benchmark with over 5,000 question-answer pairs across 288 real indoor-scene videos sourced from ScanNet, ScanNet++, and ARKitScenes. The benchmark evaluates multimodal LLMs on eight spatial tasks spanning three categories: configurational (object count, relative distance, relative direction, route plan), measurement estimation (object size, room size, absolute distance), and spatiotemporal (appearance order).

## Contributions

- VSI-Bench: a large-scale video-based benchmark specifically targeting visual-spatial intelligence with 5,000+ QA pairs and 8 task types
- A taxonomy of visual-spatial intelligence capabilities (visual perception, linguistic intelligence, temporal processing, spatial reasoning)
- Evidence that spatial reasoning — not perception or language — is the primary bottleneck for MLLMs (71% of errors)
- Discovery that MLLMs form local but not global spatial world models, as shown through cognitive map probing
- Demonstration that linguistic prompting techniques (CoT, ToT) are harmful for spatial tasks, while cognitive map generation improves distance reasoning

## Method

The benchmark is constructed from existing 3D reconstruction datasets (ScanNet, ScanNet++, ARKitScenes) that provide accurate object-level 3D annotations. QA pairs are generated through a combination of question templates (using meta-information like object bounding boxes) and human annotation (route plan task). A human-in-the-loop quality review process iteratively removes ambiguous or incorrect questions.

For evaluation, MCA (multiple-choice answer) tasks use standard accuracy, while NA (numerical answer) tasks use Mean Relative Accuracy (MRA), which averages relative accuracy across confidence thresholds from 0.5 to 0.95.

The cognitive map probing prompts MLLMs to predict object center positions within a 10×10 grid based on video input, enabling quantitative assessment of the model's internal spatial representation.

## Datasets & evaluation

- Human evaluators achieve 79% average accuracy; best MLLM (Gemini-1.5 Pro) achieves 45.4%
- Humans dominate configurational/spatiotemporal tasks (94-100%), but the gap narrows on measurement estimation
- Top open-source models (LLaVA-Video-72B, LLaVA-OneVision-72B) trail Gemini-1.5 Pro by only 4-5%
- 7 of 12 open-source models perform below chance level
- CoT prompting reduces average performance by ~4%; self-consistency still falls 1.1% below baseline
- Cognitive map prompting improves relative distance accuracy from 46% to 56%; ground-truth maps yield 66-78%

## Limitations

- The benchmark is limited to indoor scenes; outdoor and large-scale environments remain untested
- Cognitive maps are evaluated on a coarse 10×10 grid — finer-grained spatial representations need exploration
- The study identifies the bottleneck (spatial reasoning) but does not propose a training-time solution
- Route planning is the only human-annotated task; scaling human annotation remains challenging
- Whether fine-tuning on spatial tasks or developing spatial-specific architectures can close the human-model gap is an open question

## Key takeaways

The authors conduct a comprehensive evaluation of 15 MLLMs and find that while spatial reasoning capabilities are emerging, a large gap remains between models and humans (79% vs. 45% average accuracy for the best model, Gemini-1.5 Pro). Critically, the paper provides deep analysis into *why* models fail through two complementary probes: linguistic self-explanations and visual cognitive maps. Self-explanation analysis reveals that 71% of errors stem from spatial reasoning deficits (relational reasoning and egocentric-allocentric transformation), not from perception, language understanding, or temporal processing.

The cognitive map analysis reveals that MLLMs build strong local world models (64% accuracy for adjacent objects) but weak global ones — accuracy degrades dramatically with increasing object distance. Standard prompting techniques (CoT, self-consistency, tree-of-thoughts) all hurt spatial performance, but explicitly generating cognitive maps before answering improves relative distance accuracy by 10%, suggesting that spatial world models are a promising avenue for improvement.

Unlike prior spatial reasoning benchmarks that use 2D images or text-only evaluation, VSI-Bench uses real-world video input, more closely mirroring how humans and embodied agents perceive space. The benchmark repurposes 3D reconstruction datasets (ScanNet, ScanNet++, ARKitScenes) to get ground-truth spatial annotations, bridging the gap between 3D scene understanding and MLLM evaluation. The finding that linguistic prompting techniques fail for spatial tasks contrasts sharply with their success on general video understanding benchmarks like VideoMME.
