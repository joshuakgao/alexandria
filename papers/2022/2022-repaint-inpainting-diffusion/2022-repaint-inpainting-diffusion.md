---
title: "RePaint: Inpainting using Denoising Diffusion Probabilistic Models"
authors: [Andreas Lugmayr, Martin Danelljan, Andres Romero, Fisher Yu, Radu Timofte, Luc Van Gool]
year: 2022
venue: "CVPR"
tags: [image-inpainting, diffusion-models]
url: ""
pdf: "[[2022-repaint-inpainting-diffusion.pdf]]"
date_ingested: 2026-06-18
---

# RePaint: Inpainting using Denoising Diffusion Probabilistic Models

![[2022-repaint-inpainting-diffusion-thumbnail.png]]

## Research gap

RePaint is a landmark work demonstrating that pre-trained unconditional denoising diffusion probabilistic models (DDPMs) can be effectively adapted for image inpainting without task-specific training. The key insight is that inpainting can be formulated as a sampling strategy problem: during reverse diffusion iterations, simply replace noisy unmasked regions with noisy versions of the known image, while allowing the model to freely generate content in masked regions. This approach requires no model retraining or fine-tuning, enables generalization to arbitrary mask distributions (including extreme masks), and leverages the full semantic generation capability of powerful DDPM priors. Additionally, RePaint introduces an improved "repainting" strategy that goes forward and backward through diffusion time, progressively refining inpainting to achieve semantically meaningful results. Extensive experiments on CelebA-HQ and ImageNet demonstrate superiority over GAN and autoregressive methods.

## Contributions

- **DDPM-based inpainting framework**: First to successfully use pre-trained unconditional DDPM as primary mechanism for inpainting
- **Training-free adaptation**: No model retraining or fine-tuning required; leverages existing DDPM checkpoints
- **Mask-distribution generalization**: Generalizes to arbitrary mask types without training on specific distributions
- **Repainting strategy**: Novel iterative refinement that goes forward/backward in diffusion time for semantic generation
- **Diverse outputs**: Stochastic generation enables sampling multiple diverse inpainting results

## Method

**Core inpainting strategy**:

1. **Standard diffusion reversal with conditioning**: Start from random noise zT, iteratively denoise
2. **Masking-based conditioning**: At each reverse step t:
   - Generate noisy content for masked region using diffusion model
   - Replace unmasked region with noisy version of known image
   - Repeat denoising step for semantic refinement
3. **Repainting refinement**: Forward-backward iterations through diffusion time
   - Forward: add noise to intermediate result
   - Backward: denoise again with updated conditioning
   - Progressively refine inpainting while maintaining known regions

**Key properties**:
- No model modification required
- Works with any DDPM architecture
- Mask-agnostic: generalizes across all mask types
- Leverages semantic priors learned by pre-trained models

## Datasets & evaluation

- **Mask generalization**: State-of-the-art on multiple mask distributions including extreme cases
- **CelebA-HQ**: Outperforms GAN and autoregressive methods on face inpainting
- **ImageNet**: Strong results on diverse object/scene inpainting
- **Diversity**: Can generate multiple semantically coherent inpainting results
- **Comparison**: Surpasses existing methods on 5 out of 6 mask distribution benchmarks

## Limitations

- How does performance scale to very high-resolution images?
- Can the approach be extended to video inpainting with temporal consistency?
- How sensitive is repainting quality to the number of forward-backward iterations?
- Can repainting be combined with additional guidance (text, reference images)?

## Key takeaways

RePaint builds on diffusion models for generation but is novel in the inpainting domain. While prior work (Sohl-Dickstein et al., Song et al.) explored diffusion for inpainting theoretically, RePaint is first to demonstrate practical state-of-the-art results and introduced the repainting strategy that enables semantic generation. It contrasts with training-based approaches (encoder-decoder GANs with partial convolutions, gated convolutions) and shows diffusion's advantages for semantic reasoning.
