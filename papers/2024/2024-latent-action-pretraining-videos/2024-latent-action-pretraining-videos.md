---
title: "Latent Action Pretraining from Videos"
authors: [Seonghyeon Ye, Joel Jang, Byeongguk Jeon, Sejune Joo, Jianwei Yang, Baolin Peng, Ajay Mandlekar, Reuben Tan, Yu-Wei Chao, Yuchen Lin, Lars Liden, Kimin Lee, Jianfeng Gao, Luke Zettlemoyer, Dieter Fox, Minjoon Seo]
year: 2024
venue: "ICLR 2025"
tags: [vision-language-action, embodied-ai]
url: "https://arxiv.org/abs/2410.11758"
date_ingested: 2026-07-29
---

# Latent Action Pretraining from Videos

![[2024-latent-action-pretraining-videos-thumbnail.png]]

## Research gap
Existing Vision-Language-Action (VLA) models require ground-truth robot action labels during pretraining, typically collected through expensive human teleoperation. This severely limits the scale and diversity of pretraining data. Internet video offers abundant examples of manipulation and physical interaction, but lacks action labels and has a fundamentally different distribution from robotic systems.

## Contributions
- First unsupervised method for pretraining VLA models without ground-truth robot action labels, enabling use of internet-scale video data.
- A two-stage pretraining pipeline: (1) a VQ-VAE-based latent action quantization model that learns discrete latent actions between video frames, and (2) a latent VLA pretrained to predict these latent actions from observations and language instructions.
- Demonstrates that LAPA outperforms OpenVLA (trained with ground-truth actions) on real-world manipulation tasks requiring language conditioning, generalization to unseen objects, and semantic generalization to unseen instructions.
- Shows positive transfer from pretraining on human manipulation videos alone, opening a path to web-scale robotics foundation models.

## Method
LAPA consists of three stages:

1. **Latent Action Quantization**: A VQ-VAE encoder-decoder model learns discrete latent actions from pairs of consecutive video frames. The encoder takes current frame x_t and future frame x_{t+H} and outputs a quantized latent action z_t. The decoder reconstructs x_{t+H} from z_t and x_t using cross-attention. NSVQ is used to avoid gradient collapse, and stop-gradient is applied to patch embeddings during decoding to prevent representation collapse.

2. **Latent Pretraining**: The encoder from stage 1 serves as an inverse dynamics model, labeling all video frames with latent actions. A pretrained VLM (7B LWM-Chat-1M) is then trained to predict latent actions given language instructions and current observations. A separate latent action head (single MLP) is attached for prediction. The vision encoder is frozen; the language model is unfrozen.

3. **Action Finetuning**: The latent action head is discarded and replaced with a new action head that maps to ground-truth robot actions (delta end-effector). The model is finetuned on a small set of labeled robot trajectories. Continuous actions are discretized with equal-frequency binning.

## Datasets & evaluation
- **Language Table** (simulation): 5 subtask categories testing in-domain, cross-task, and cross-environment transfer. LAPA significantly outperforms Scratch, UniPi, and VPT baselines across all settings.
- **SIMPLER** (simulation): 4 manipulation tasks with WidowX arm. LAPA outperforms VPT and OpenVLA baselines.
- **Real-world tabletop manipulation**: 3 tasks (pick-and-place, cover with towel, knock over) on Franka Panda arm with 150 trajectories per task across 15 objects. LAPA (Open-X) achieves 50.1% average success vs. OpenVLA's 43.9%, outperforming on unseen object combinations, unseen objects, and unseen instructions.
- **Human video pretraining**: LAPA pretrained solely on Something-Something v2 human manipulation videos achieves 34.0% average success, outperforming models pretrained on BridgeV2 robot data (30.8%), demonstrating positive transfer from human video.

## Limitations
- Grasping performance lags behind coarse planning — early grasping failures are common, likely because grasp events are rare in training trajectories.
- The latent action quantization model conditions only on two frames (no history) due to computational constraints.
- Action finetuning still requires a small set of ground-truth labeled trajectories.
- The approach has not yet been tested at true web-scale (pretraining used curated datasets, not raw internet video).

## Key takeaways
- Latent actions learned via VQ-VAE provide a universal, embodiment-agnostic representation that transfers better across embodiments than ground-truth action labels, which can cause overfitting to source action spaces.
- Pretraining without action labels can outperform pretraining with action labels — LAPA surpasses OpenVLA despite never seeing ground-truth actions during pretraining, achieving +6.2% improvement with 30x greater pretraining efficiency.
- Human manipulation video is a viable pretraining source for robot policies, suggesting that web-scale video data could substantially advance robotics foundation models.
- The latent action decoder can serve as a world model, enabling neural closed-loop rollouts for evaluation without physical robot execution.
