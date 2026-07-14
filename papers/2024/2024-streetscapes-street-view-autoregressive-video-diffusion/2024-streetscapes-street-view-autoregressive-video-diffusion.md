---
title: "Streetscapes: Large-scale Consistent Street View Generation Using Autoregressive Video Diffusion"
authors: [Boyang Deng, Richard Tucker, Zhengqi Li, Leonidas Guibas, Noah Snavely, Gordon Wetzstein]
year: 2024
venue: "SIGGRAPH 2024"
tags: [world-models, autonomous-vehicles, image-video-generation]
url: "https://arxiv.org/abs/2407.13759"
date_ingested: 2026-07-08
---

# Streetscapes: Large-scale Consistent Street View Generation Using Autoregressive Video Diffusion

![[2024-streetscapes-street-view-autoregressive-video-diffusion-thumbnail.png]]

## Research gap
Existing video generation and 3D view synthesis methods cannot scale to long-range, city-scale camera trajectories while maintaining visual quality and 3D consistency. Text-to-video models are limited to short clips, text-to-3D methods handle individual objects but not whole cities, and GAN-based approaches like InfiniCity suffer from limited quality and diversity. There is no method that can generate consistent street-level views spanning multiple city blocks with controllable layout and appearance.

## Contributions
- A system for generating long-range, 3D-consistent street view sequences conditioned on overhead maps, height maps, and text prompts.
- A layout-conditioned two-frame video diffusion model using G-buffer rendering from map data combined with ControlNet and motion modules.
- A novel temporal imputation method for autoregressive video generation that prevents quality drift over long sequences by replacing random noise inputs with noised versions of previously generated frames.
- Demonstration that large-scale street view datasets (Google Street View) with corresponding map data can serve as compelling training data for generative urban scene synthesis.

## Method
The system builds on a pretrained latent diffusion model (Stable Diffusion) with three key components:

1. **Two-frame motion module**: Inspired by AnimateDiff, temporal attention layers are inserted into the U-Net to enable joint generation of two consecutive frames, ensuring local consistency.

2. **G-buffer ControlNet conditioning**: Input street maps and height maps are rendered from desired camera poses into screen-space G-buffers (semantic labels, disparity, height). A ControlNet conditions generation on these G-buffers, providing layout and camera pose control without explicit pose parameters.

3. **Temporal imputation for autoregressive generation**: To extend beyond two frames without quality drift, the method replaces the standard parallel denoising (starting from random noise) with an imputation approach. The two noise inputs are replaced by noised copies of the last generated frame and a warped version of that frame into the next viewpoint. This keeps the generation on the manifold of natural images over long sequences. Resampling and WarpInit techniques further improve near-range consistency.

Training uses a two-stage procedure: first training motion modules (1M iterations), then training the ControlNet (500K iterations), both with batch size 256. An optional 3D reconstruction step can produce explicit scene geometry from the generated frames.

## Datasets & evaluation
- **Training data**: Google Street View imagery from 23 cities across 8 countries, with corresponding OpenStreetMap data and aerial height maps. ~67K street segments used for training.
- **Baselines**: InfiniCity (GAN-based), InfNat0 (GAN-based), Infinite Nature (depth-warping + refinement), DiffDreamer (diffusion-based depth warping).
- **Metrics**: FID, KID (image quality), LPIPS (consistency between frames), and user studies.
- **Key results**: Streetscapes significantly outperforms all baselines on both image quality (FID/KID) and consistency (LPIPS). The temporal imputation approach prevents the quality degradation that plagues alternative autoregressive methods, maintaining stable FID/KID even after 64 generation steps. User studies strongly preferred Streetscapes over baselines for both quality and consistency.

## Limitations
- Relies on coarse aerial height maps and street maps that can be noisy and misaligned with ground-level imagery, requiring robustness to data noise.
- Camera poses are approximate (using latitude/longitude with estimated capture vehicle height), leading to imperfect G-buffers.
- Privacy-blurred regions in source data must be filtered out.
- The system generates 2D video sequences rather than explicit 3D models (though optional 3D reconstruction is possible).
- Limited to the street view domain and data distribution of the training cities.

## Key takeaways
- Autoregressive temporal imputation — using noised copies of condition frames rather than conditioning on clean frames directly — is critical for preventing quality drift in long-range video generation. This insight generalises beyond street view to any autoregressive diffusion video setting.
- Coarse geometric conditioning (maps + height maps rendered as G-buffers) provides sufficient control for city-scale generation without requiring precise 3D models or per-object annotations.
- Large-scale geographic datasets like Google Street View, paired with freely available map data, represent an underutilised resource for training generative models of urban environments.
- The approach bridges video generation and world modelling: it generates 3D-consistent imagery of environments that can be explored with controllable camera trajectories, conditioned on layout and text.
