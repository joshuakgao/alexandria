---
title: "CogVideo: Large-scale Pretraining for Text-to-Video Generation via Transformers"
authors: [Wenyi Hong, Ming Ding, Wendi Zheng, Xinghan Liu, Jie Tang]
year: 2022
venue: "ICLR 2023"
tags: [image-video-generation]
url: "https://arxiv.org/abs/2205.15868"
date_ingested: 2026-06-30
---

# CogVideo: Large-scale Pretraining for Text-to-Video Generation via Transformers

![[2022-cogvideo-text-to-video-generation-thumbnail.png]]

## Research gap
Autoregressive transformers had achieved strong results in text-to-image generation (DALL-E, CogView), but extending them to text-to-video generation faced two key challenges: (1) the prohibitive compute cost of training from scratch, and (2) poor text-video alignment caused by scarce, weakly-relevant text-video datasets and naive fixed-frame-rate training that breaks the semantic correspondence between text descriptions and video clips.

## Contributions
- CogVideo: a 9.4B-parameter transformer for open-domain text-to-video generation, the first open-source large-scale pretrained model for this task
- Dual-channel attention mechanism that efficiently inherits knowledge from a pretrained text-to-image model (CogView2) without destroying its learned representations, avoiding expensive training from scratch
- Multi-frame-rate hierarchical training and generation strategy that improves text-video semantic alignment by adapting frame rates to match text descriptions with complete actions
- Extension of Swin attention to autoregressive video generation, enabling parallel token generation across frames for faster inference

## Method
CogVideo builds on CogView2 (a pretrained text-to-image transformer) using three key techniques:

**Dual-channel attention**: Each transformer layer adds a new temporal attention channel (attention-plus) alongside the frozen CogView2 spatial attention (attention-base). The two channels are mixed via a learned sigmoid gate and share the same FFN, preserving image generation knowledge while learning temporal dynamics. Only the new temporal channel parameters are trainable — the 6B CogView2 parameters remain frozen.

**Multi-frame-rate hierarchical training**: Instead of splitting videos into fixed-frame-rate clips (which destroys text-action alignment), training samples use the lowest frame rate that captures the complete action described by the text. A frame-rate token prepended to the text controls the intensity of inter-frame changes. Generation proceeds in two stages: (1) sequential generation of 5 key frames at a low frame rate, then (2) recursive frame interpolation at progressively higher frame rates using CogLM's bidirectional attention for context-aware infilling.

**3D Swin attention for autoregressive generation**: Shifted window attention is extended to autoregressive video generation with causal masking, enabling parallel generation of tokens in distant spatial regions across frames. This provides up to floor(XY / (Ax*Y + Ay)) tokens of parallelism.

The model uses VQVAE tokenization (400 tokens per 160x160 frame), with sequences of 2,065 tokens (64 text + 5x400 image + 1 separator). Output can be upsampled to 480x480 via CogView2's super-resolution.

## Datasets & evaluation
- Trained on 5.4M captioned videos at 160x160 resolution
- **UCF-101**: After finetuning, achieves IS 50.46 and FVD 626 (FVD 545 with tokenizer-reconstructed ground truth). Outperforms VideoGPT, DVD-GAN, MoCoGAN-HD, and DIGAN on IS; competitive FVD with TATS-base (79.28 IS, 332 FVD)
- **Kinetics-600**: FVD 109.23 (59.55 with reconstructed GT), outperforming all baselines including Video Transformer, DVD-GAN-FP, and TriVD-GAN-FP
- **Human evaluation**: 90 evaluators rated CogVideo best overall (49.53% preference), significantly outperforming VideoGPT (15.42%) and TGANv2 (5.6%) on frame texture, motion realism, and semantic relevance
- **Ablations**: Hierarchical generation outperforms 1-stage sliding window; CogView2 initialization outperforms random initialization; dual-channel attention with frozen parameters trains faster and more stably

## Limitations
- Restricted input sequence length due to model scale and GPU memory constraints, limiting video duration and resolution
- Training data is 160x160 resolution, requiring a separate super-resolution model for higher quality
- Text inputs during pretraining were in Chinese, potentially limiting English text-video alignment
- FVD scores lag behind specialized models trained directly on target datasets (e.g., TATS-base on UCF-101)
- No explicit temporal modeling beyond the learned attention patterns — complex multi-step actions may still be challenging

## Key takeaways
- Inheriting from pretrained text-to-image models is far more efficient than training text-to-video models from scratch — dual-channel attention preserves image knowledge while learning temporal dynamics with only the new channel's parameters
- Frame rate is a critical but often overlooked variable in video generation training: matching frame rate to action duration dramatically improves text-video semantic alignment, especially for complex actions like "drinking water"
- The multi-frame-rate hierarchical generation (low-rate key frames followed by recursive interpolation) is a powerful paradigm that later influenced video generation pipelines including Stable Video Diffusion and GAIA-1
- CogVideo demonstrated that the autoregressive transformer paradigm scales from text-to-image to text-to-video generation, establishing a path that led to CogVideoX and other large-scale video generation models
