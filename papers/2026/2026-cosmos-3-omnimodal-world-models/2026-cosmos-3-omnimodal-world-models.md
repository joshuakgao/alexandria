---
title: "Cosmos 3: Omnimodal World Models for Physical AI"
authors: [NVIDIA]
year: 2026
tags: [world-models, embodied-ai, image-video-generation]
url: "https://arxiv.org/abs/2606.02800"
date_ingested: 2026-07-29
---

# Cosmos 3: Omnimodal World Models for Physical AI

![[2026-cosmos-3-omnimodal-world-models-thumbnail.png]]

## Research gap
Physical AI agents require multiple coupled capabilities — perception, reasoning, simulation, and action — that are currently served by fragmented model pipelines: separate VLMs for understanding, video generators for world simulation, and VLAs/WAMs for action prediction. This fragmentation is computationally wasteful, prevents shared representation learning, and creates integration complexity. Additionally, scaling training data and environments for Physical AI remains a bottleneck, with no unified model capable of jointly generating high-fidelity synthetic data, simulating world dynamics, and producing robot control signals.

## Contributions
- **Omnimodal world model architecture**: Cosmos 3 jointly processes and generates language, image, video, audio, and action within a single Mixture-of-Transformers (MoT) framework. Depending on input-output configuration, it seamlessly operates as a VLM, text-to-image/video generator, audio-visual generator, forward/inverse dynamics model, or robot policy — without architectural modifications.
- **Mixture-of-Transformers (MoT) backbone**: A dual-tower layer structure where autoregressive (AR) tokens for reasoning and diffusion (DM) tokens for generation are processed by independent parameter sets (LayerNorms, MLPs) but interact through shared joint attention. Both towers are initialized from pretrained VLM weights (Qwen3-VL), inheriting strong language and visual reasoning.
- **Unified action representation**: A domain-aware action tokenization that maps heterogeneous embodiments (autonomous vehicles, cameras, single/dual-arm robots, humanoids, egocentric human motion) into compact action vectors built from shared geometric components (relative SE(3) poses + grasp states), with domain-specific projection layers sharing the MoT backbone.
- **Three action generation modes**: Forward dynamics (predict future video from actions), inverse dynamics (infer actions from video transitions), and policy mode (jointly predict video and actions) — all within the same model by varying which tokens are clean vs. noisy during diffusion.
- **State-of-the-art across understanding and generation**: Ranked #1 open-source text-to-image (Artificial Analysis), #1 robot policy (RoboArena, MolmoSpaces), competitive with Gemini 3.1 Pro on reasoning, and best open-source on PAIBench-G video generation.
- **Three model scales**: Edge (4B), Nano (16B), and Super (64B), all open-sourced with code, checkpoints, synthetic datasets, and evaluation benchmarks.

## Method
**Architecture**: Cosmos 3 uses a Mixture-of-Transformers (MoT) where each transformer layer has two parameter towers: a reasoner tower for autoregressive (AR) tokens and a generator tower for diffusion (DM) tokens. The AR subsequence (language + ViT-encoded vision) uses causal self-attention; the DM subsequence (VAE-encoded video, audio, action) uses full bidirectional attention over both AR and DM keys/values. This allows diffusion tokens to condition on reasoning context while keeping AR tokens autoregressively self-contained. Both towers are initialized from pretrained Qwen3-VL weights.

**Encoders**: Modality-specific encoders project inputs into a shared representation space: (1) ViT encoder (with DeepStack aggregation) for visual understanding, (2) Wan2.2 video VAE for visual generation (frozen, 4× temporal / 32×32 spatial compression), (3) audio VAE (frozen, 25 tokens/sec), (4) domain-aware action projections mapping heterogeneous embodiments into a shared latent space.

**Action representation**: Actions encode transitions between consecutive video states using three components: ego poses (agent observation frame, as relative SE(3) deltas with 6D rotation), effector poses (end-effector deltas), and grasp states (current manipulation state). Domain-specific input/output projection layers handle different action vector lengths while sharing the backbone. Supports camera motion (9D), autonomous vehicles (9D), single-arm robots (10D), dual-arm robots (29D), humanoids (20D), and egocentric human motion (57D).

**Token arrangement**: All tasks use a unified format: AR tokens first, then DM tokens. Within DM, clean conditioning tokens precede noisy target tokens. Language tokens are generated via next-token prediction; all other modalities via iterative flow-matching denoising. Three action modes are supported by varying clean/noisy assignments: forward dynamics (clean actions, noisy video), inverse dynamics (clean video, noisy actions), policy (both noisy).

**Position embedding**: 3D Multimodal RoPE with absolute temporal modulation. FPS modulation aligns tokens from different temporal resolutions (video, audio, action at different sampling rates) onto a shared physical time axis. A fixed temporal gap (15,000) between AR and DM subsequences prevents artifacts from adjacent positional embeddings.

**Training recipe**:
1. *Reasoner pre-training*: ~22M samples (image-text, video-text, text-only) for broad visual understanding.
2. *Reasoner SFT*: ~2.2M samples specialized for Physical AI (robotics, driving, smart infrastructure), with 50% video-text.
3. *Generator pre-training*: Large-scale image, video, and audio data using flow-matching loss.
4. *Generator mid-training*: Adds action modality from diverse sources (cameras, autonomous vehicles, robots, egocentric human motion), plus curated synthetic data (SDG-PhyxSim, SDG-RobotSim, SDG-DriveSim, SDG-SynHuman, SDG-Warehouse).
5. *Post-training*: Task-specific specialization — text-to-image, image-to-video, robot policy (DROID) — without architectural modifications.

**Infrastructure**: Trained on NVIDIA H100/B200 clusters. Serving via PyTorch, vLLM, TensorRT-LLM, and vLLM-Omni with optimizations including torch compile + CUDA graphs, context parallelism (Ulysses), CFG parallelism, Cache-DiT, FP8 quantization, and reasoner tower caching.

## Datasets & evaluation
**Training data**: Reasoner: ~24.2M samples. Generator: large-scale image/video/audio corpus plus action data from autonomous driving, camera motion, robotic manipulation (Google Robot, WidowX-250, Franka Panda, AgiBot, DROID), and egocentric human motion. Five curated synthetic datasets (SDG-PhyxSim/RobotSim/DriveSim/SynHuman/Warehouse) generated via NVIDIA Omniverse.

**Evaluation — Reasoner** (48 benchmarks across 4 categories):
- General (19 benchmarks): Cosmos3-Super achieves 73.7 avg, competitive with Qwen3-VL-32B (72.8) and trailing Gemini 3.1 Pro (77.5).
- Robotics (17 benchmarks): 57.8 avg, outperforming all open-source models and approaching Gemini 3.1 Pro (58.2).
- Smart infrastructure (9 benchmarks): 62.6 avg, best among all models including Gemini 3.1 Pro (58.6).
- Driving (3 benchmarks): 79.3 avg, best among all models.

**Evaluation — Generator**:
- Text-to-image: Cosmos3-Super-Text2Image achieves 91.36 on UniGenBench (best overall), #1 open-weight on Artificial Analysis arena.
- Text-to-video / Image-to-video: Cosmos3-Super achieves 80.0/82.8 on PAIBench-G (best open-source, outperforming Veo-3.1 on T2V).
- Robot policy (Cosmos3-Nano-Policy-DROID): 39.7% success on RoboLab (vs. π0.5 at 28.1%, DreamZero at 25.2%), #1 on RoboArena and MolmoSpaces leaderboards.
- Fast adaptation: MT-init reaches 24.6% success at 500 iterations on LIBERO-10 (vs. 0% for PT-init), 97.4% at 2000 iterations.
- Action synergy: Cross-domain action training shows positive transfer — robot domains benefit from co-training with camera/vehicle motion data.

## Limitations
- Trails Gemini 3.1 Pro on general reasoning benchmarks (73.7 vs. 77.5), suggesting the dual-tower architecture's generation capacity may come at some cost to pure reasoning performance.
- The technical report does not provide detailed ablations on whether unified omnimodal training produces better results than specialized models on any single capability — the claimed benefit is convenience and shared representations, not necessarily peak per-task performance.
- Audio generation evaluation is limited (single metric reported), making it difficult to assess audio quality comprehensively.
- Real-world robot evaluation is limited to DROID-platform manipulation; generalization to other embodiments (humanoids, quadrupeds) is demonstrated only in simulation or video generation, not closed-loop control.
- The policy mode (joint video-action prediction) is not evaluated against dedicated world-action models in controlled settings, leaving unclear whether unified training helps or hurts policy quality versus specialized alternatives.

## Key takeaways
- A single omnimodal architecture can subsume VLMs, video generators, world simulators, and robot policies without architectural modifications — the key enabler is the MoT dual-tower design where reasoning (AR) and generation (DM) share attention but use independent parameters, both initialized from a pretrained VLM.
- Action as a first-class modality, with unified geometric representation across embodiments (SE(3) pose deltas + grasp states) and domain-specific projection layers, enables a single model to handle autonomous driving, camera control, and dexterous manipulation simultaneously.
- The three action generation modes (forward dynamics, inverse dynamics, policy) emerge naturally from varying which tokens are clean vs. noisy during diffusion training — no separate model heads or task-specific losses are needed.
- Cross-domain action data synergy is real: robot manipulation domains consistently benefit from co-training with camera motion and autonomous driving data, suggesting shared visual-dynamics priors transfer across embodiments.
- Mid-training initialization (MT-init) from diverse action data dramatically accelerates adaptation to new embodiments (24.6% vs. 0% at 500 iterations on LIBERO-10), confirming that omnimodal pretraining provides a strong foundation for downstream specialization.
- Post-training specialization from a shared omnimodal checkpoint achieves SOTA on multiple independent leaderboards simultaneously (Artificial Analysis T2I, RoboArena, MolmoSpaces), demonstrating that unified pretraining does not sacrifice peak task-specific performance.
