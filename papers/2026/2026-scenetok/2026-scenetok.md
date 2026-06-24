---
title: "SceneTok: A Compressed, Diffusable Token Space for 3D Scenes"
authors: [Mohammad Asim, Christopher Wewer, Jan Eric Lenssen]
year: 2026
venue: "CVPR 2026"
tags: [world-models, diffusion-models, image-generation]
url: ""
pdf: "[[2026-scenetok.pdf]]"
date_ingested: 2026-06-18
---

# SceneTok: A Compressed, Diffusable Token Space for 3D Scenes

![[2026-scenetok-thumbnail.png]]

## Research gap

SceneTok introduces the first tokenizer that compresses 3D scenes from multi-view images into a small set of unstructured, permutation-invariant tokens — disentangled from any spatial grid or 3D data structure. This compressed representation is 1–3 orders of magnitude smaller than competing representations (32K floats vs. 1.57M for LVSM, 46M for Gaussian splatting methods) while achieving state-of-the-art novel view synthesis quality. The tokens can be efficiently rendered from novel trajectories using a lightweight rectified flow decoder (32 images/second), and crucially, the compressed space enables simple latent diffusion-based scene generation in just 5 seconds.

## Contributions

- First 3D scene tokenizer producing an unstructured, highly compressed token set (1024 tokens × 32 dims = 32K floats) disentangled from spatial grids
- A generative rectified flow decoder that handles uncertainty gracefully, enabling true novel view synthesis (not just interpolation) from arbitrary trajectories
- Decoupling of scene generation from view rendering, enabling independent scaling and a much better quality-speed tradeoff than view-space generation
- SceneGen: a latent diffusion transformer for conditional scene generation in 5 seconds, competitive with large-scale multi-view and video generators
- 1–3 orders of magnitude compression over prior representations while achieving SOTA reconstruction quality (PSNR, rFVD, rFID)

## Method

**Encoder (SceneTok):** Context views are first compressed by a frozen VA-VAE encoder (16x spatial compression). The resulting latent patches are fed into a scene perceiver module with two branches: (1) a multi-view branch that processes patch tokens with AdaLN ray-map modulation and multi-view attention, and (2) a scene token branch with learnable queries that performs self-attention and cross-attention to the multi-view branch. Output: K=1024 tokens of dimension d=32. Uses 2D RoPE (not 3D) for order invariance.

**Decoder:** A rectified flow transformer conditioned on scene tokens (via cross-attention) and novel camera ray maps (via AdaLN). Iteratively denoises latent image patches, then decodes to pixels via frozen VideoDCAE decoder from Open-Sora 2.0. Renders 32 views in 1 second.

**SceneGen (Stage 2):** A diffusion transformer trained on encoded scene tokens using rectified flows. Conditioned on a single input image and camera anchor poses defining the spatial extent. Generates scene tokens in 5 seconds, which are then rendered by the frozen decoder.

**Training:** End-to-end rectified flow matching in latent space on RealEstate10K and DL3DV. 4× A100 GPUs, ~250 GPU-hours for SceneTok, ~238 GPU-hours for SceneGen.

## Datasets & evaluation

**Novel View Synthesis (RealEstate10K, 12 context views):** PSNR 23.99 (vs. LVSM 21.25, DepthSplat 21.55), LPIPS 0.159, rFVD 79.80 (vs. LVSM 211.66), rFID 11.12 — all with 32K floats vs. LVSM's 1.57M.

**Transferability:** Superior TPS (True-Pose-Similarity) on novel trajectories vs. LVSM and RayZer, confirming true NVS rather than view interpolation. RayZer fails transferability due to information leakage from target views.

**Zero-shot ACID:** Generalizes to unseen outdoor scenes without fine-tuning, achieving rFVD 246.51 vs. LVSM 522.57.

**Scene Generation (SceneGen):** Competitive with DFoT and DFM in FID/FVD while being orders of magnitude faster (5s vs. minutes). SEVA achieves better FID/FVD but uses closed-source large-scale data.

**Uncertainty:** Variance of rendered outputs correlates with information content of scene tokens — the decoder adapts its sampling distribution based on conditioning strength.

## Limitations

- Scene quality is bounded by the frozen video VAE (VA-VAE encoder, VideoDCAE decoder); better VAEs would directly improve SceneTok
- SceneGen produces slightly less high-frequency detail than pixel-space generators (DFoT) or models trained on larger closed-source data (SEVA)
- Scaling to larger scenes (outdoor, city-scale) beyond indoor/room-scale is not demonstrated
- The fixed number of scene tokens (K=1024) may be suboptimal — adaptive token counts based on scene complexity could improve efficiency
- Multi-object dynamic scenes are not addressed; extension to 4D (temporal) scene tokenization is future work
- The relationship between token count, scene complexity, and reconstruction quality saturates at K=1024 due to overfitting

## Key takeaways

The method follows a two-stage approach: (1) an autoencoder (SceneTok) that encodes context views into scene tokens via a scene perceiver module and decodes them with a generative rectified flow renderer, and (2) a diffusion transformer (SceneGen) trained on the encoded token space for conditional scene generation. This decouples scene generation from view rendering — the generative model operates in a highly compressed latent space while the lightweight decoder handles rendering, enabling independent scaling of each component.

Key to the design is that the decoder is generative (rectified flow-based), allowing it to gracefully handle uncertainty — sampling from narrow distributions for well-defined regions and falling back to generation for uncertain areas. The encoder uses 2D RoPE (not 3D) to avoid temporal bias, ensuring scene tokens are order-invariant and can be rendered from arbitrary trajectories, not just interpolations along the input trajectory.

SceneTok sits at the intersection of vision tokenizers (VQGAN, Latent Diffusion), feed-forward 3D reconstruction (LVSM, RayZer, MVSplat), and scene generation (DFM, DFoT, SEVA). It differs from explicit 3D representations (Gaussians, NeRFs) which are high-dimensional and hard to generate, and from latent representations like LVSM which use 3K+ high-dimensional tokens making diffusion infeasible. The generative decoder contrasts with deterministic renderers in Gaussian splatting methods — the stochastic sampling is essential for handling compression-induced uncertainty. The 2D RoPE finding (vs. 3D RoPE) reveals that temporal ordering in the encoder creates harmful bias for scene representations that should be view-order invariant.
