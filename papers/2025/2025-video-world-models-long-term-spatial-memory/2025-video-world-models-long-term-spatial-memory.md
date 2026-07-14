---
title: "Video World Models with Long-term Spatial Memory"
authors: [Tong Wu, Shuai Yang, Ryan Po, Yinghao Xu, Ziwei Liu, Dahua Lin, Gordon Wetzstein]
year: 2025
venue: "NeurIPS 2025"
tags: [world-models, image-video-generation, 3d-scene-reconstruction]
url: "https://arxiv.org/abs/2506.05284"
date_ingested: 2026-07-08
---

# Video World Models with Long-term Spatial Memory

![[2025-video-world-models-long-term-spatial-memory-thumbnail.png]]

## Research gap
Autoregressive video world models suffer from severe forgetting when revisiting previously generated scenes, due to limited temporal context windows. Existing approaches either increase context length (computationally expensive), compress distant frames (losing information), or ground generation in 3D point clouds (but struggle with dynamic scenes). No prior work combines persistent 3D spatial memory with dynamic scene handling for long-term consistent world generation.

## Contributions
- A memory framework for video world models inspired by human memory, combining three types: short-term working memory (recent context frames), long-term spatial memory (static 3D point cloud via TSDF fusion), and long-term episodic memory (sparse historical keyframes).
- A spatial memory storage mechanism that incrementally builds a global static point cloud using CUT3R for online 4D reconstruction with TSDF fusion to filter out dynamic elements.
- A memory-guided video generation architecture built on CogVideoX-5B, using ControlNet-style conditioning from rendered point clouds, context frame concatenation, and historical cross-attention for episodic memory retrieval.
- A geometry-grounded video dataset of 90K structured samples from MiraData with paired 3D spatial memory and future observations for training and evaluation.

## Method
The framework augments a CogVideoX-5B diffusion transformer with three memory mechanisms:

1. **Working memory**: The most recent 5 generated frames are concatenated with target frames along the temporal dimension, providing short-term dynamic context for smooth motion continuity.

2. **Spatial memory**: A persistent global static point cloud is maintained across the generation process. At each autoregressive step, CUT3R performs online 4D reconstruction of newly generated frames, producing per-frame point maps and camera poses in a shared coordinate system. TSDF fusion integrates these observations, inherently filtering out dynamic elements (inconsistent depth across frames yields low-confidence voxels). The static point cloud is rendered from target camera poses and encoded via the pretrained 3D VAE as conditioning latents. A ControlNet-style condition DiT (first 18 blocks copied from CogVideoX) processes these latents and injects them via zero-initialized linear layers.

3. **Episodic memory**: A sparse set of historical keyframes is stored when newly revealed unknown regions exceed a visibility threshold. These frames are encoded and patchified as reference tokens, then attended to via historical cross-attention layers (video tokens as queries, reference tokens as keys/values) added to the main DiT.

Training uses 90K video samples from MiraData, each 97 frames (49 source + 48 target), with Mega-SAM for camera/depth estimation during dataset construction and Qwen for action text annotation. Training runs for 6K iterations on 8×A100 GPUs at 480×720 resolution.

## Datasets & evaluation
- **Training data**: 90K structured video samples from MiraData with paired 3D spatial memory, processed via Mega-SAM and TSDF fusion.
- **Test set**: 500 randomly selected unseen MiraData sequences.
- **Baselines**: TrajectoryCrafter, DiffusionAsShader (DaS), Wan2.1-Inpainting.
- **Metrics**: View recall consistency (PSNR/SSIM/LPIPS on reversed trajectory revisits), VBench (aesthetic quality, imaging quality, temporal flickering, motion smoothness, subject/background consistency), and user study (20 expert participants ranking camera accuracy, static consistency, dynamic plausibility).
- **Key results**: Significantly outperforms all baselines on view recall (PSNR 19.10 vs. best baseline 12.16; LPIPS 0.3069 vs. 0.5874). Top overall VBench scores. User study shows large margins across all three criteria (average ranking 3.4–3.6 vs. best baseline 2.4–2.7 on 1–4 scale). Ablations confirm each memory type contributes: working memory is critical for motion coherence, episodic memory for visual detail retention, spatial memory for geometric consistency.

## Limitations
- TSDF fusion introduces artifacts when viewing previously generated content from significantly different camera angles than original observations.
- When distance between consecutive camera poses is too large or trajectories have abrupt angles, CUT3R's 4D reconstruction fails, causing ghosting artifacts and overly sparse spatial memory.
- Does not address quality drift (error accumulation over time in autoregressive generation).
- Spatial memory primarily handles static scene elements; dynamic object consistency relies only on the short working memory window.
- Trained and evaluated only on MiraData videos; generalization to diverse real-world domains is not demonstrated.

## Key takeaways
- Human memory taxonomy (spatial, working, episodic) provides a productive inductive bias for structuring long-term memory in video world models — each type addresses a distinct failure mode (geometric drift, motion discontinuity, visual detail forgetting).
- Geometry-grounded 3D point clouds with TSDF fusion provide an effective mechanism for persistent spatial memory that naturally separates static from dynamic scene elements, enabling consistent view recall during scene revisits.
- The combination of all three memory types is essential — ablations show that removing any one degrades performance, with working memory most critical for motion and episodic memory most critical for long-term visual details.
- Even with all memory mechanisms, perfect view recall remains very challenging (PSNR of 19.10 on revisits), indicating that remembering every visual detail of complex scenes is an open problem.
