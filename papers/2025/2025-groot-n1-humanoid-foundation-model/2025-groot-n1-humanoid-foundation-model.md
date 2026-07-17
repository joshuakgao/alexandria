---
title: "GR00T N1: An Open Foundation Model for Generalist Humanoid Robots"
authors: [NVIDIA]
year: 2025
venue: ""
tags: [embodied-ai, vision-language-action]
url: "https://arxiv.org/abs/2503.14734"
date_ingested: 2026-07-14
---

# GR00T N1: An Open Foundation Model for Generalist Humanoid Robots

![[2025-groot-n1-humanoid-foundation-model-thumbnail.png]]

## Research gap
Training generalist humanoid robot policies faces a fundamental data scarcity problem — no internet-scale humanoid dataset exists, and the great variability across robot embodiments creates fragmented "data islands" rather than a coherent training corpus. Existing VLA models do not address how to effectively combine heterogeneous data sources (real robot trajectories, human videos, synthetic data) into a unified training pipeline for humanoid robots.

## Contributions
- GR00T N1, an open 2.2B-parameter VLA foundation model for humanoid robots with a dual-system architecture: a VLM reasoning module (System 2, Eagle-2) coupled with a diffusion transformer action module (System 1) via cross-attention.
- A data pyramid training strategy that organizes heterogeneous data sources by scale — web/human videos at the base, synthetic data in the middle, real robot demonstrations at the top — enabling effective co-training across all layers.
- Techniques for learning from action-less data: latent-action codebooks and inverse dynamics models (IDM) to infer pseudo-actions from human videos and neural-generated trajectories.
- Neural trajectory augmentation: fine-tuning video generation models on robot data to produce counterfactual training videos, with IDM-labeled pseudo-actions, yielding consistent gains in both simulation and real-world tasks.
- Open-source release of the 2B model checkpoint, training data, and simulation benchmarks.

## Method
GR00T N1 uses Eagle-2 (1.34B parameters) as the VLM backbone to process image observations and language instructions into tokens. A diffusion transformer (DiT) cross-attends to these VLM tokens and generates motor actions via flow-matching at 120Hz, while the VLM runs at 10Hz. Embodiment-specific MLP encoders/decoders handle variable state and action dimensions across robot morphologies. Training follows two phases: (1) pre-training on the full data pyramid — real robot data (GR-1 humanoid, Open X-Embodiment, AgiBot-Alpha), simulation trajectories (540K demonstrations via DexMimicGen), human videos (Ego4D, EPIC-KITCHENS, etc.) with latent-action labels, and ~827 hours of neural trajectories from fine-tuned video generation models; (2) post-training on embodiment-specific data with optional neural trajectory augmentation via 1:1 co-training. The VLM language component stays frozen throughout; vision encoder and DiT are fine-tuned. Pre-training used ~50,000 H100 GPU hours.

## Datasets & evaluation
Evaluated on three simulation benchmarks: RoboCasa Kitchen (24 tasks, Franka arm), DexMimicGen Cross-Embodiment Suite (9 tasks, bimanual + humanoid), and GR-1 Tabletop Tasks (24 tasks, humanoid with dexterous hands). GR00T N1 achieves 45.0% average success vs. 33.4% for Diffusion Policy and 26.4% for BC-Transformer with 100 demos per task. On real-world GR-1 humanoid benchmarks (pick-and-place, articulated manipulation, industrial tasks, multi-agent coordination), GR00T N1 achieves 76.8% average success with full data vs. 46.4% for Diffusion Policy — a 30.4% improvement. With only 10% of data, GR00T N1 (42.6%) nearly matches Diffusion Policy trained on full data (46.4%), demonstrating strong data efficiency. Neural trajectory augmentation adds +4-9% in simulation and +5.8% on real-world tasks.

## Limitations
Currently limited to short-horizon tabletop manipulation tasks — long-horizon loco-manipulation remains future work. Neural trajectory generation still struggles with physical fidelity, limiting diversity and counterfactual quality. Post-training on task-specific data can erase pre-trained capabilities (e.g., inter-hand transfer lost after right-hand-only fine-tuning). The model focuses on the Fourier GR-1 humanoid platform for real-world experiments.

## Key takeaways
- The data pyramid strategy — layering web videos, synthetic data, and real demonstrations — is an effective approach to the humanoid data scarcity problem, enabling pre-training that transfers to real-world humanoid tasks with 76.6% zero-shot success on novel object manipulation.
- The dual-system architecture (slow VLM reasoning + fast DiT action generation) achieves practical inference: 63.9ms for 16-action chunks on an L40 GPU, enabling real-time 120Hz control.
- Neural trajectory augmentation provides consistent gains across data regimes, with IDM-labeled actions outperforming latent actions when sufficient data is available for IDM training.
- Cross-embodiment pre-training is key — GR00T N1's largest gains over baselines appear on the GR-1 humanoid benchmark (+17% over Diffusion Policy), where pre-training on diverse embodiments provides the strongest transfer.
