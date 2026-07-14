---
title: "Video Diffusion Models"
authors: [Jonathan Ho, Tim Salimans, Alexey Gritsenko, William Chan, Mohammad Norouzi, David Fleet]
year: 2022
venue: "NeurIPS 2022"
tags: [image-video-generation]
url: "https://arxiv.org/abs/2204.03458"
date_ingested: 2026-06-30
---

# Video Diffusion Models

![[2022-video-image-video-generation-thumbnail.png]]

## Research gap
Diffusion models had achieved high-quality results for image and audio generation, but had not been demonstrated for video generation. Extending diffusion models to video required addressing temporal coherence, memory constraints from 3D data, and the need to generate long sequences beyond the fixed training window.

## Contributions
- Presents the first diffusion model for video generation, extending the standard image diffusion formulation with minimal modifications to a 3D U-Net architecture with factorized space-time attention.
- Introduces reconstruction-guided sampling, a new conditional generation method that corrects the replacement/imputation method by adding a gradient term based on the model's reconstruction of conditioning data, enabling temporally coherent autoregressive video extension and spatial super-resolution.
- Demonstrates that joint training on image and video data reduces minibatch gradient variance and significantly improves sample quality on both modalities.
- Achieves state-of-the-art results on unconditional video generation (UCF-101) and video prediction (Kinetics-600, BAIR Robot Pushing), and presents the first results on text-conditioned video generation.

## Method
The architecture is a 3D U-Net that factorizes space and time: 2D convolutions become space-only 1×3×3 convolutions, spatial attention blocks are retained, and temporal attention blocks (with relative position embeddings) are inserted after each spatial attention block. This factorization enables masking the model to process independent images by disabling temporal attention, supporting joint image-video training. Training uses the standard ε-prediction diffusion objective with a cosine noise schedule. For generating videos longer than the training window (16 frames), reconstruction-guided sampling adjusts the denoising model output with a gradient term: x̃_θ^b(z_t) = x̂_θ^b(z_t) − (w_r α_t / 2) ∇_{z_t^b} ‖x_a − x̂_θ^a(z_t)‖², where x_a is the conditioning data (previous frames or low-resolution video) and w_r is a guidance weight. This can be combined with predictor-corrector samplers using Langevin correction steps for improved results. The same reconstruction guidance method extends to spatial super-resolution by applying the loss through a differentiable downsampling operator.

## Datasets & evaluation
Unconditional generation on UCF-101 (16×64×64): FVD 69.89, substantially outperforming prior methods (DVD-GAN: 32.97 FVD on class-conditional, but 69.15 IS vs VDM's 57.00 FVD). Video prediction on Kinetics-600 (5→11 frames, 64×64): FVD 16.97, outperforming FitVid (93.56), CCVS (105.26), and others. Video prediction on BAIR Robot Pushing (1→15 frames, 64×64): FVD 16.48. Text-conditioned generation on a large-scale dataset (16×64×64): joint image-video training with 8 image frames per video reduces FVD from 202.28 to 57.84 and FID from 37.52 to 15.57. Classifier-free guidance improves IS metrics at the cost of diversity. Reconstruction guidance dramatically outperforms the replacement method for autoregressive extension (FVD 136.22 vs. 451.45 on 64-frame generation).

## Limitations
- Models are trained on only 16 frames at low resolution (64×64 or 128×128), requiring cascaded super-resolution and autoregressive extension for practical video generation.
- Sampling is slow due to the iterative denoising process over 3D data — even slower than image diffusion models due to the temporal dimension.
- Reconstruction guidance requires backpropagation through the denoising model at each sampling step, adding significant computational overhead.
- Text-conditioned results are preliminary and at low resolution; the model was not released due to concerns about misuse.
- The Gaussian approximation underlying reconstruction guidance degrades for large noise levels, though empirically it works well.

## Key takeaways
- Video diffusion models require surprisingly little architectural modification from image diffusion models — factorized space-time attention in a 3D U-Net suffices, demonstrating the generality of the diffusion framework across data modalities.
- Joint image-video training is a key practical insight: it reduces gradient variance (treating independent images as single-frame videos), improves both image and video quality metrics, and allows leveraging larger image datasets to benefit video generation.
- Reconstruction guidance solves a fundamental problem with conditional sampling from unconditional diffusion models: the standard replacement/imputation method fails to account for the gradient of the conditioning likelihood, leading to temporal incoherence. The reconstruction guidance correction is general and applies to temporal extension, frame interpolation, and spatial super-resolution.
- This work established the template (3D U-Net, factorized attention, cascaded generation, classifier-free guidance) that subsequent video generation systems (Imagen Video, Make-A-Video, Sora) built upon.
