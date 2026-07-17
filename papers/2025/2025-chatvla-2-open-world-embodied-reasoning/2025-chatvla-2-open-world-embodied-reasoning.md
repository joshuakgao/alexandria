---
title: "ChatVLA-2: Vision-Language-Action Model with Open-World Embodied Reasoning from Pretrained Knowledge"
authors:
  - Zhongyi Zhou
  - Yichen Zhu
  - Junjie Wen
  - Chaomin Shen
  - Yi Xu
year: 2025
venue: ""
tags:
  - embodied-ai
  - vision-language-action
url: "https://arxiv.org/abs/2505.21906"
date_ingested: 2026-07-14
---

# ChatVLA-2: Vision-Language-Action Model with Open-World Embodied Reasoning from Pretrained Knowledge

![[2025-chatvla-2-open-world-embodied-reasoning-thumbnail.png]]

## Research gap
Current VLA models lose the pre-trained reasoning and understanding capabilities of their VLM backbones during robot fine-tuning. This means they perform well on in-domain manipulation tasks but fail in open-world scenarios requiring novel reasoning (e.g., unseen math problems, unfamiliar objects, new spatial relations). Existing approaches treat multi-modal understanding and robot control as competing objectives within a shared dense parameter space.

## Contributions
- A dynamic Mixture-of-Experts (MoE) architecture integrated into the VLM backbone that disentangles multi-modal understanding from robot control, preserving pre-trained knowledge during fine-tuning.
- A reasoning-following enhancement module that replaces observation embeddings with reasoning tokens in the latter-half layers of the diffusion action expert, enabling actions to follow open-world reasoning outputs.
- A two-stage training strategy: (1) co-training on image-text and robot data to establish open-world reasoning, then (2) freezing the VLM and training only the action expert to strengthen reasoning-action alignment.
- Demonstration of substantial open-world generalization — 82.7% success on unseen math equations and 81.4% on novel spatial reasoning tasks, representing a 3.5x improvement over DexVLA in open-world settings.

## Method
ChatVLA-2 builds on DexVLA's architecture: a Qwen2-VL backbone paired with a 1B-parameter ScaleDP diffusion action expert. The key addition is a dynamic MoE module (8 experts, top-2 routing) on top of the VLM's MLP layers, initialized from pre-trained weights. Dynamic MoE is chosen over static/shared experts because the latter would alter the LLM's architecture and rapidly degrade pre-trained knowledge. The reasoning-following enhancement module projects VLM reasoning tokens through an MLP to condition scale and shift parameters in the action expert's latter-half layers — injecting reasoning context without destabilizing action generation. Training stage 1 co-trains on COCO, TextVQA, GQA, and robot data (600 math-game + 300 toy-placement trajectories) at a 1:3 image-text-to-robot ratio for 15k steps. Stage 2 freezes the VLM and trains only the action expert for 50k steps to strengthen reasoning-following. Total training cost is 340 GPU hours.

## Datasets & evaluation
Evaluated on two real-robot scenarios: a math matching game (ARX-R5 bimanual, 14-DoF) and a toy placement task (Franka with Robotiq gripper, 7-DoF). In-domain, ChatVLA-2 performs comparably to DexVLA and pi-0. In open-world settings, ChatVLA-2 achieves 43/52 (82.7%) success on unseen math equations vs. 10/52 for DexVLA and 8/52 for pi-0. On open-world toy placement, it achieves 81.4% success — 3.52x DexVLA's rate. OCR accuracy reaches 3.58/4 and math reasoning 1.73/2 in open-world, compared to near-zero for all baselines. Ablations confirm that both dynamic MoE and two-stage training are essential: removing stage 1 eliminates open-world reasoning; removing stage 2 drops success to 23%; a dense 7B model without MoE also fails in open-world scenarios.

## Limitations
Pre-trained VLM knowledge is not fully preserved — some capabilities are inevitably lost during robot fine-tuning. Evaluation is limited to tabletop manipulation tasks; mobile manipulation and long-horizon tasks are not addressed. The approach has not been tested on diverse embodiments beyond the two platforms used.

## Key takeaways
- Dynamic MoE is critical for preserving VLM pre-trained knowledge in VLA models — it disentangles task-specific and shared features, preventing the parameter-space competition that causes dense models to lose reasoning capabilities.
- The two-stage training strategy (co-train for reasoning, then freeze VLM for action alignment) enables open-world generalization that no prior VLA achieves, moving from zero to meaningful performance on unseen reasoning tasks.
- Injecting reasoning into the latter-half layers of the action expert is significantly more effective than full-layer or former-half injection, consistent with findings from PointVLA and GR00T N1 that deeper layers are less sensitive to modifications.
