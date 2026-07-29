---
title: "DreamDojo: A Generalist Robot World Model from Large-Scale Human Videos"
authors: [Shenyuan Gao, William Liang, Kaiyuan Zheng, Ayaan Malik, Seonghyeon Ye, Sihyun Yu, Wei-Cheng Tseng, Yuzhu Dong, Kaichun Mo, Chen-Hsuan Lin, Qianli Ma, Seungjun Nah, Loic Magne, Jiannan Xiang, Yuqi Xie, Ruijie Zheng, Dantong Niu, You Liang Tan, K.R. Zentner, George Kurian, Suneel Indupuru, Pooya Jannaty, Jinwei Gu, Jun Zhang, Jitendra Malik, Pieter Abbeel, Ming-Yu Liu, Yuke Zhu, Joel Jang, Linxi Fan]
year: 2026
venue: "ICML 2026"
tags: [world-models, embodied-ai]
url: "https://arxiv.org/abs/2602.06949"
date_ingested: 2026-07-29
---

# DreamDojo: A Generalist Robot World Model from Large-Scale Human Videos

![[2026-dreamdojo-thumbnail.png]]

## Research gap
Existing video world models for robotics are confined to in-distribution settings — trained and evaluated on narrow robot datasets with limited object diversity, scene variety, and action coverage. High-dimensional continuous action spaces for contact-rich dexterous tasks remain poorly modeled because robot data is expensive to collect, hardware-specific, and dominated by expert demonstrations lacking stochastic action diversity. Meanwhile, the vast majority of human interaction videos on the internet lack action labels, making it unclear how to transfer their rich physics knowledge to action-conditioned world models.

## Contributions
- **DreamDojo-HV dataset**: A 44,711-hour egocentric human video dataset — the largest and most diverse corpus for world model pretraining — spanning 6,015+ skills, 43,237+ objects, and 9,869+ scenes across household, industrial, retail, and other daily environments.
- **Continuous latent actions as unified proxy**: A self-supervised latent action model (700M spatiotemporal Transformer VAE) that extracts semantically meaningful action representations from consecutive frames via an information bottleneck, enabling action-conditioned pretraining on videos without ground-truth action labels. Latent actions transfer across embodiments (human hands ↔ robot arms).
- **Foundation world model**: DreamDojo (2B and 14B variants), built on Cosmos-Predict2.5, pretrained on the human video mixture with latent action conditioning. Architectural innovations include relative action transformation (rebaselining to per-latent-frame origins) and chunked causal action injection, plus a temporal consistency loss for improved dynamics modeling.
- **Distillation pipeline**: A Self Forcing-based distillation that converts the bidirectional diffusion teacher into an autoregressive, 4-step student model achieving 10.81 FPS at 640×480 — enabling real-time interaction with improved long-horizon consistency via extended rollout training.
- **Downstream applications**: Live teleoperation (real-time VR-controlled virtual robot), policy evaluation (Pearson r=0.995 correlation with real-world success rates), and model-based planning (up to 2× success rate improvement via action proposal selection).

## Method
**Architecture**: Built on Cosmos-Predict2.5 (latent video diffusion with DiT blocks, WAN2.2 tokenizer). Two key modifications for action controllability: (1) relative actions — rebaselined to the pose at the beginning of each latent frame (every 4 timesteps), concentrating actions in a narrower shared space; (2) chunked causal action injection — 4 consecutive actions are concatenated and injected only into their corresponding latent frame via adaptive layer normalization, enforcing temporal causality.

**Latent action model**: A 700M spatiotemporal Transformer VAE with 24 encoder + 24 decoder blocks. The encoder takes two consecutive frames and produces a 32-dimensional latent action vector; the decoder reconstructs the next frame from the latent action and the current frame. The information bottleneck (compact embedding + KL regularization with β=10⁻⁶) forces disentanglement of action from context. Trained on all human and robot datasets jointly with random temporal downsampling (1–4×).

**Training recipe**:
1. *Pretraining* (140K steps, batch 1024, 256 H100s): Train on In-lab + EgoDex + DreamDojo-HV (sampling ratio 1:2:10) with latent action conditioning. Training objective combines flow matching loss with a temporal consistency loss (λ=0.1) that supervises inter-frame velocity transitions.
2. *Post-training* (50K steps, batch 512, 128 H100s): Fine-tune on target robot data (e.g., GR-1, G1, AgiBot) with reinitialized first action MLP layer to adapt to the target action space. All weights are updated.
3. *Distillation*: Replace bidirectional attention with causal attention over a 12-frame sliding window. Warmup stage regresses student to teacher ODE solutions (10K iterations). Distillation stage trains student on its own generated context with distribution matching loss (3K iterations), generating 13–49 frames and supervising a random 13-frame window.

**Action MLP**: Projects latent or real actions to match timestep embedding dimensions. Last layer zero-initialized to avoid perturbing pretrained weights. During post-training, first layer is reinitialized for the new action space.

## Datasets & evaluation
**Training data**: 44,711 hours total — In-lab (55h, tabletop with Manus gloves + Vive tracker), EgoDex (829h, Apple Vision Pro egocentric manipulation), DreamDojo-HV (43,827h, crowdsourced daily activities). 15× longer duration, 96× more skills, and 2,000× more scenes than the previously largest world model dataset.

**Evaluation benchmarks**: Six out-of-distribution sets constructed on Fourier GR-1: (1) In-lab Eval — novel objects matching human video; (2) EgoDex Eval — novel objects from EgoDex; (3) DreamDojo-HV Eval — diverse daily objects; (4) Counterfactual Eval — actions absent from robot training (patting, missing); (5–6) EgoDex-novel / DreamDojo-HV-novel Eval — AI-edited backgrounds replicating human dataset distributions. Metrics: PSNR, SSIM, LPIPS for automatic evaluation; 12-volunteer human preference evaluation for physics correctness and action following.

**Key results**:
- Latent action pretraining matches retargeted ground-truth actions on In-lab (PSNR 20.913 vs. 20.960) and approaches MANO on EgoDex (20.344 vs. 20.474), while being fully scalable.
- Increasing data diversity consistently improves all benchmarks — In-lab+EgoDex+DreamDojo-HV yields best results across ID and OOD evaluations.
- DreamDojo-14B wins 73.5% (physics) and 72.6% (action following) human preference over Cosmos-Predict2.5 baseline.
- Distilled student achieves 10.81 FPS (vs. teacher's 2.72 FPS) with minor degradation on 600-frame (1-minute) rollouts.
- Policy evaluation: Pearson r=0.995, MMRV=0.003 correlation with real-world success rates on AgiBot fruit packing.
- Model-based planning: Up to 17% success rate improvement over best single checkpoint; ~2× over uniform sampling.

## Limitations
- Struggles with uncommon actions (slapping, fast waving) due to insufficient coverage in training data.
- Policy evaluation success rates are systematically higher than real-world counterparts, indicating difficulty in accurately generating nuanced failure modes.
- Does not support multi-view simulation, which is important for state-of-the-art robot policies.
- Knowledge retention during post-training is not studied — fine-tuning may degrade pretrained physics understanding.
- Inference speed, while real-time, could benefit from further engineering optimizations.

## Key takeaways
- Human egocentric video is a highly effective pretraining source for robot world models — the underlying physics of interactions transfers across the embodiment gap, provided action conditioning is maintained during pretraining.
- Continuous latent actions solve the action label scarcity problem at internet scale: self-supervised extraction from frame pairs produces semantically meaningful, cross-embodiment action representations that match ground-truth action conditioning in downstream performance.
- Data diversity matters more than data scale alone — adding more diverse human datasets consistently improves both physics modeling and action controllability on out-of-distribution scenarios.
- Chunked causal action injection and relative action transformation are critical architectural choices for precise continuous action controllability in video diffusion world models.
- Real-time autoregressive distillation via Self Forcing enables practical deployment of diffusion world models for live teleoperation and online planning, with extended rollout training improving long-horizon consistency.
- World models pretrained on diverse human data can serve as reliable policy evaluation proxies (r=0.995 with real-world) and effective model-based planners (~2× success improvement), demonstrating practical value beyond data generation.
