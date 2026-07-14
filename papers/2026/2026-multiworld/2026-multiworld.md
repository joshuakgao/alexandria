---
title: "MultiWorld: Scalable Multi-Agent Multi-View Video World Models"
authors: [Haoyu Wu, Jiwen Yu, Yingtian Zou, Xihui Liu]
year: 2026
tags: [world-models, multi-agent, image-video-generation]
url: "https://arxiv.org/abs/2604.18564"
date_ingested: 2026-07-05
---

# MultiWorld: Scalable Multi-Agent Multi-View Video World Models

![[2026-multiworld-thumbnail.png]]

## Research gap
Existing video world models assume single-agent scenarios and cannot handle the complex interactions inherent in multi-agent systems. They also fail to maintain visual consistency across multiple viewpoints that different agents observe from a shared environment. No prior work addresses multi-agent controllability, multi-view consistency, and scalability to variable agent/view counts in a unified framework.

## Contributions
- A unified framework (MultiWorld) for multi-agent, multi-view video world modeling that scales to arbitrary numbers of agents and camera views
- Multi-Agent Condition Module (MACM) with Agent Identity Embedding (using RoPE) and Adaptive Action Weighting for precise multi-agent controllability
- Global State Encoder (GSE) using a frozen VGGT backbone to extract 3D-aware environmental states from partial observations, ensuring multi-view consistency
- Two complementary datasets: a 100-hour real-player dataset from ItTakesTwo and a multi-robot manipulation dataset from RoboFactory

## Method
MultiWorld decomposes multi-view simulation into parallel single-view video generation tasks sharing a global environment state. Built on Flow Matching with a Transformer (DiT) backbone (Wan2.2-5B):

1. **Multi-Agent Condition Module (MACM):** Actions are embedded into latent tokens, then Agent Identity Embedding (via Rotary Position Embedding along the agent dimension) resolves identity ambiguity. Self-attention models inter-agent interactions. Adaptive Action Weighting (learned via MLP) dynamically prioritizes active agents over stationary ones, producing a unified per-frame action token injected via causal cross-attention.

2. **Global State Encoder (GSE):** A frozen VGGT (3D reconstruction foundation model) encodes multi-view observations into 3D-aware latent features, which are projected via MLP and injected into the DiT via cross-attention. This ensures spatial consistency across views without explicit 3D reconstruction.

3. **Scalable autoregressive generation:** Videos from different views are generated in parallel. The global state is updated iteratively from the last generated frames, enabling long-horizon simulation beyond training context length.

## Datasets & evaluation
- **ItTakesTwo:** 100 hours of real-player dual-agent gameplay at 60fps (21M+ frames at 2560×1440)
- **RoboFactory:** Multi-robot manipulation with 2–4 agents and variable camera views

Key results (vs. Standard, Concat-View, COMBO baselines):
- Video game: FVD 179 (vs. 207–245), action-following 89.8% (vs. 88.4–89.3%), RPE 0.67 (vs. 0.72–0.75)
- Robotics: FVD 96 (vs. 99–106), comparable or better across metrics
- Ablations confirm MACM improves action controllability and GSE improves multi-view consistency

## Limitations
- Trained at relatively low resolution (320×320 per view)
- Evaluation limited to game and robotics domains; generalization to open-world settings not demonstrated
- Relies on frozen VGGT which may not capture all environment dynamics
- Autoregressive long-horizon generation still shows some degradation beyond 2× training context

## Key takeaways
- Decomposing multi-agent multi-view world modeling into parallel single-view generation with a shared 3D-aware global state is an effective and scalable design
- RoPE-based agent identity embedding elegantly resolves the symmetry problem in multi-agent action conditioning
- Using a pretrained 3D reconstruction model (VGGT) as a frozen encoder for global state provides strong multi-view consistency without explicit 3D supervision
- The framework achieves ~1.5× speedup over sequential view generation through parallelism
