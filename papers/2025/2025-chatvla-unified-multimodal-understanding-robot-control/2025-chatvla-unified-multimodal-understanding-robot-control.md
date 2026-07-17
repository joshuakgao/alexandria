---
title: "ChatVLA: Unified Multimodal Understanding and Robot Control with Vision-Language-Action Model"
authors:
  - Zhongyi Zhou
  - Yichen Zhu
  - Minjie Zhu
  - Junjie Wen
  - Ning Liu
  - Zhiyuan Xu
  - Weibin Meng
  - Ran Cheng
  - Yaxin Peng
  - Chaomin Shen
  - Feifei Feng
year: 2025
venue: "EMNLP 2025"
tags:
  - vision-language-action
  - embodied-ai
url: "https://arxiv.org/abs/2502.14420"
date_ingested: 2026-07-14
---

# ChatVLA: Unified Multimodal Understanding and Robot Control with Vision-Language-Action Model

![[2025-chatvla-unified-multimodal-understanding-robot-control-thumbnail.png]]

## Research gap
Existing vision-language-action (VLA) models prioritize robotic action mastery but lose the multimodal understanding capabilities of their underlying vision-language model (VLM) backbones during fine-tuning. The authors identify two root causes: (1) spurious forgetting, where robot training overwrites visual-text alignment without truly erasing knowledge, and (2) task interference, where control and understanding tasks compete within shared parameter spaces when co-trained.

## Contributions
- Systematic analysis of three VLA training paradigms (robot-only, robot+reasoning, robot+visual-text co-training), revealing how each affects multimodal understanding and control performance.
- Identification and naming of "spurious forgetting" — showing that VLA fine-tuning disrupts alignment rather than erasing knowledge, and that conversational ability can be reactivated with small amounts of visual-text data.
- Phased Alignment Training: a two-stage curriculum where the model first masters embodied control, then co-trains with visual-text data to recover understanding capabilities.
- A Mixture-of-Experts (MoE) architecture on MLP layers that isolates task-specific representations (control vs. understanding) while sharing attention layers for cross-task knowledge transfer.

## Method
ChatVLA builds on a 2B-parameter Qwen2-VL backbone with a LoRA-adapted vision encoder, a diffusion-based action head, and an LLM head for text output. The key innovations are:

1. **Phased Alignment Training** — Stage 1 trains on robot demonstration data with reasoning phrases to build control proficiency while maintaining visual-text alignment. Stage 2 co-trains on both visual-text pairs and robot data to reactivate multimodal understanding.
2. **Mixture-of-Experts** — Each transformer block's MLP is replaced with two expert pathways: a control expert (activated for robot data) and an understanding expert (activated for visual-text data). Self-attention layers are shared across both tasks, motivated by Dual Coding Theory, enabling knowledge transfer between physical skills and visual-linguistic understanding. During inference, only one pathway is active, preserving base model parameter count.

Task routing is controlled by distinct system prompts ("Predict robot action" vs. "Answer based on question").

## Datasets & evaluation
- **Multimodal understanding**: Evaluated on 13 benchmarks including MMMU, MMStar, MME, OCRBench, HallBench, MMBench, TextVQA, DocVQA, InfoVQA, AI2D, ChartQA, MTVQA, and RealWorldQA.
- **Robot control**: 25 real-world manipulation tasks across tabletop, kitchen, and bathroom environments, covering skills like picking, placing, pushing, hanging, and stacking.
- **Key results**: 6x improvement over ECoT on MMMU (37.4 vs. 5.4); MMStar boosted from 0 to 47.2 with 3.5x fewer VLM backbone parameters. On long-horizon robot tasks, ChatVLA significantly outperforms Octo and OpenVLA (e.g., 0.94 avg. success length vs. 0.31 for OpenVLA on a 4-step task).

## Limitations
- Robot experiments use a single Franka Emika Panda arm; generalization to other embodiments is untested.
- The static MoE routing (based on system prompt) is less flexible than learned routing and assumes clean task separation at inference time.
- The 2B parameter model, while more efficient than 7B alternatives, still has a gap to larger VLMs on some understanding benchmarks.
- Evaluation is limited to single-arm tabletop manipulation; more complex locomotion or bimanual tasks are not addressed.

## Key takeaways
- VLA fine-tuning causes "spurious forgetting" rather than true catastrophic forgetting — the knowledge is recoverable with targeted reactivation through visual-text co-training.
- Sharing attention layers while isolating MLPs via MoE is an effective architectural pattern for multi-task learning across embodied control and language understanding.
- A unified model that can both chat and act is feasible at the 2B parameter scale, challenging the assumption that separate specialist models are needed for understanding and control.
