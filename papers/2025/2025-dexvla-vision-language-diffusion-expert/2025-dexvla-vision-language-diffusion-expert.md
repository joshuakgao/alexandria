---
title: "DexVLA: Vision-Language Model with Plug-In Diffusion Expert for General Robot Control"
authors: [Junjie Wen, Yichen Zhu, Jinming Li, Zhibin Tang, Chaomin Shen, Feifei Feng]
year: 2025
venue: "CoRL 2025"
tags: [embodied-ai, vision-language-action]
url: "https://arxiv.org/abs/2502.05855"
date_ingested: 2026-07-14
---

# DexVLA: Vision-Language Model with Plug-In Diffusion Expert for General Robot Control

![[2025-dexvla-vision-language-diffusion-expert-thumbnail.png]]

## Research gap
Existing vision-language-action (VLA) models prioritize scaling the vision-language model (VLM) backbone while treating the action representation as a secondary concern. This architectural imbalance limits dexterous manipulation capabilities. Additionally, current VLAs require massive datasets (thousands of hours) and rely on external high-level policies (e.g., SayCan) to complete long-horizon tasks, rather than reasoning end-to-end.

## Contributions
- A billion-parameter diffusion-based action expert with a multi-head architecture for cross-embodiment learning, a significant scale-up from conventional multi-million parameter action modules.
- An embodied curriculum learning strategy with three stages: (1) cross-embodiment pre-training of the diffusion expert, (2) embodiment-specific VLA alignment, and (3) task-specific adaptation.
- Sub-step reasoning training that enables the VLA to decompose and complete long-horizon tasks (e.g., laundry folding) without an external high-level policy.
- Demonstration of strong performance across single-arm, bimanual, dexterous hand, and mobile bimanual robots using only 100 hours of data.

## Method
DexVLA combines a Qwen2-VL 2B vision-language backbone with a 1B-parameter diffusion-based action expert. The diffusion expert uses a multi-head architecture where each head corresponds to a specific embodiment, enabling cross-embodiment learning. Training follows a three-stage curriculum: (1) pre-training the diffusion expert alone on cross-embodiment data using a ResNet-50 image encoder and DistilBERT language encoder, (2) aligning the full VLA model to specific embodiments by connecting the VLM to the diffusion expert, and (3) task-specific post-training for rapid adaptation. For long-horizon tasks, demonstrations are annotated with sub-step reasoning (e.g., "fold the shirt" decomposed into "smooth wrinkles," "align sleeves," "secure folds"), enabling the model to learn disentangled action representations without needing an external high-level planner.

## Datasets & evaluation
Evaluated across four robot configurations: Franka with gripper, Franka with dexterous hand, bimanual UR5e, and mobile AgileX. The model was pre-trained on approximately 100 hours of demonstration data spanning 91 tasks. Tasks include shirt folding, bin picking, drink pouring with a dexterous hand, and packing with bimanual arms. DexVLA achieves near-perfect scores on flattened shirt folding, learns dexterous skills with fewer than 100 demonstrations on novel embodiments, and outperforms state-of-the-art models like OpenVLA and pi-0 on long-horizon tasks. Runs at 60Hz on a single Nvidia A6000 GPU.

## Limitations
Performance degrades on very long-horizon, contact-rich scenarios such as folding crumpled shirts. The multi-head architecture requires embodiment-specific heads, limiting zero-shot transfer to entirely new morphologies. The sub-step reasoning annotations rely on automated labeling (Grounding-DINO, Gemini 2.0), which may introduce noise.

## Key takeaways
- Scaling the action expert (rather than only the VLM backbone) is a viable and effective strategy for improving VLA models.
- A curriculum learning approach enables data-efficient training — 100 hours of data suffices for strong cross-embodiment performance, compared to thousands of hours for competing methods.
- Integrating sub-step reasoning directly into VLA training eliminates the need for external high-level planners, making the system more end-to-end.
