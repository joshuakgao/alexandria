---
title: "ShareVerse: Collaborative Video Generation for Shared World Modeling"
authors: [Jiayi Zhu, Jianing Zhang, Yiying Yang, Wei Cheng, Xiaoyun Yuan]
year: 2026
tags: [world-models, multi-agent, image-video-generation, autonomous-vehicles]
url: "https://arxiv.org/abs/2603.02697"
date_ingested: 2026-07-02
---

# ShareVerse: Collaborative Video Generation for Shared World Modeling

![[2026-shareverse-collaborative-video-shared-world-thumbnail.png]]

## Research gap
Existing video generation models produce high-quality single-agent perspectives but cannot collaboratively render a unified, spatiotemporally consistent environment across multiple agents. Multi-view video synthesis methods operate under synchronous single-agent paradigms and lack the long-range memory needed when different agents visit the same location at different times. No prior framework addresses the problem of multiple distributed agents jointly synthesizing a shared, globally coherent virtual world.

## Contributions
- A collaborative video generation framework (ShareVerse) that enables multiple distributed agents to synthesize locally while maintaining global spatiotemporal consistency.
- A cross-agent collaborative attention mechanism integrated into the diffusion transformer, routing spatial features between agents to resolve visual conflicts in overlapping regions.
- A spatiotemporal memory retrieval system that anchors autoregressive generation to historically synthesized scenes, preserving environmental permanence across asynchronous agent trajectories without explicit 3D representations.
- A procedural multi-agent dataset of over 15,000 synchronized video sequences generated in CARLA, covering diverse urban topologies, weather conditions, and interaction trajectories.

## Method
ShareVerse builds on CogVideoX-5B-I2V, a diffusion transformer architecture with a 3D VAE for video latent compression. Four specialized components are added:

1. **Intra-agent spatial awareness**: Each agent uses four orthogonal cameras (front, rear, left, right) spatially concatenated into a 2x2 tiled frame. Dense geometric raymaps derived from camera intrinsics and extrinsics are injected into the DiT blocks, anchoring latent features to global 3D coordinates.

2. **Cross-agent collaborative attention**: Agent latent features are concatenated along the temporal dimension, processed through attention with RoPE, then split back and applied as residual updates. Because features are geometrically anchored via raymaps, the attention naturally aligns representations occupying the same physical space.

3. **Spatiotemporal memory retrieval**: During autoregressive generation, agents archive synthesized frames and coordinates into a spatial memory cache. For each new chunk, the agent samples future trajectory positions and retrieves the nearest historical frames from any agent's cache. A unidirectional attention mask prevents noisy generation latents from corrupting clean memory features.

4. **Training protocol**: Two-stage training — first a 49-frame base model for 15,000 steps, then fine-tuning with 33-frame chunks (29 generated + 4 memory frames) for 16,500 steps. Trained on 8 A100 GPUs with controlled noise perturbation on conditioning frames to bridge the train-inference gap.

## Datasets & evaluation
Training data is procedurally generated in CARLA across varied urban topologies, weather conditions, and agent interaction patterns (head-on encounters, intersection crossings). Over 15,000 synchronized multi-agent sequences of ~90 frames each are produced.

Evaluation uses unseen complex urban scenes where agents start with zero FOV overlap and interact asynchronously. ShareVerse outperforms baselines (Standard CogVideoX, Aether, SynCamMaster) across PSNR, SSIM, and LPIPS for cross-agent visual consistency. Qualitative results show ShareVerse accurately renders the other agent's vehicle in correct spatial locations and preserves architectural details across viewpoints.

## Limitations
- Demonstrated only in the autonomous driving domain using CARLA; generalization to other multi-agent scenarios (games, robotics) is not validated.
- Relies on known camera trajectories and poses; real-world deployment would require accurate localization.
- Two-agent scenarios are the primary focus; scaling to many agents simultaneously is not explored.
- Training requires perfectly synchronized multi-agent data, limiting applicability to domains without simulation engines.

## Key takeaways
- Shared world modeling can be achieved without explicit 3D representations by combining geometrically-anchored latent features with cross-agent attention and spatial memory retrieval.
- The spatiotemporal memory cache is crucial for asynchronous consistency — ensuring a location looks the same regardless of which agent visits it or when.
- This work opens a new research direction at the intersection of generative video models and multi-agent simulation, potentially enabling scalable virtual world synthesis beyond traditional graphics pipelines.
