---
title: "RoboScape: Physics-informed Embodied World Model"
authors: [Yu Shang, Xin Zhang, Yinzhou Tang, Lei Jin, Chen Gao, Wei Wu, Yong Li]
year: 2025
tags: [world-models, robotics]
url: "https://arxiv.org/abs/2506.23135"
date_ingested: 2026-07-29
---

# RoboScape: Physics-informed Embodied World Model

![[2025-roboscape-thumbnail.png]]

## Research gap
Current embodied world models focus on RGB pixel fitting without awareness of physical knowledge, leading to unrealistic video generation in contact-rich robotic scenarios — particularly failures in 3D geometric consistency and plausible object deformation (e.g., cloth manipulation). Existing approaches to integrating physics either rely on narrow domain-specific priors, require expensive cascaded pipelines with physics simulators, or are limited to object-level material field modeling.

## Contributions
- A unified physics-informed world model (RoboScape) that jointly learns RGB video generation, temporal depth prediction, and adaptive keypoint dynamics tracking within a single auto-regressive framework.
- An automated robotic data processing pipeline with physical prior annotations (depth maps and keypoint trajectories), built on the AGIBOT-World dataset.
- Demonstration of practical utility for downstream robotic policy training (with Diffusion Policy and pi0) and policy evaluation, with generated synthetic data matching or exceeding real data performance.

## Method
RoboScape uses a dual-branch co-autoregressive Transformer (DCT) that processes tokenized RGB and depth sequences in parallel branches, with cross-branch feature fusion injecting depth priors into RGB generation. Two physics-informed auxiliary tasks are introduced:

1. **Temporal depth prediction**: A depth branch predicts frame-level depth maps alongside RGB, with hierarchical feature fusion from the depth branch into the RGB branch via learnable linear projections, enforcing 3D geometric consistency.

2. **Adaptive keypoint dynamics learning**: SpatialTracker densely samples keypoints in the initial frame and tracks them across time. The top-K most active keypoints (by motion magnitude) are selected, and a temporal consistency loss aligns predicted tokens at keypoint locations across frames to the initial frame. A keypoint-guided attention mechanism upweights loss in high-motion regions.

Both RGB and depth branches use MAGVIT-2 for video tokenization. Robot actions are encoded and injected via additive fusion. The final loss combines RGB cross-entropy, depth cross-entropy, keypoint consistency, and attention-augmented RGB losses with tunable coefficients.

## Datasets & evaluation
- **Training data**: 50,000 video clips (~6.5M training clips after processing) from the AgiBotWorld-Beta dataset, covering 147 tasks and 72 skills.
- **Metrics**: LPIPS and PSNR (appearance fidelity), AbsRel/δ1/δ2 (geometric consistency), ΔPSNR (action controllability).
- **Results**: RoboScape outperforms IRASim, iVideoGPT, Genie, and CogVideoX across all six metrics. Best LPIPS (0.1259), PSNR (21.85), AbsRel (0.36), and ΔPSNR (3.34).
- **Policy learning**: On Robomimic Lift, Diffusion Policy trained on 200 synthetic trajectories achieves 91% success (vs. 92% with real data). On LIBERO with pi0, adding 800 synthetic trajectories to 200 real ones improves average success from 65.2% to 79.1%.
- **Policy evaluation**: Pearson correlation of 0.953 between world model evaluation and ground-truth simulator, far exceeding baselines.

## Limitations
- Relies on off-the-shelf models (Video Depth Anything, SpatialTracker) for pseudo ground-truth depth and keypoint labels, inheriting their errors.
- Keypoint dynamics learning uses a fixed top-K selection heuristic rather than learned attention to contact regions.
- Evaluation is limited to simulated robotic benchmarks; real-world transfer is not directly validated.
- The approach assumes single-camera viewpoints and does not address multi-view consistency.

## Key takeaways
- Physics-informed auxiliary tasks (depth + keypoint tracking) can be integrated into video world models without cascaded pipelines, providing complementary benefits: depth ensures geometric consistency while keypoints capture motion dynamics and implicit material properties.
- Synthetic data from physics-aware world models can approach or exceed the utility of real demonstration data for policy learning, with consistent improvement as synthetic data volume increases.
- World models with strong physical grounding can serve as reliable policy evaluators, with evaluation scores highly correlated with ground-truth simulator outcomes (Pearson r = 0.953).
