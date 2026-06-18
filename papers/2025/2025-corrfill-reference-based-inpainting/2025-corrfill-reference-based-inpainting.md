---
title: "CorrFill: Enhancing Faithfulness in Reference-based Inpainting with Correspondence Guidance in Diffusion Models"
authors: [Yuxi Liu, Jiangning Yang, Yifan Chen, Shuai Liu, Feng Lin]
year: 2025
tags: [image-inpainting, diffusion-models]
url: ""
pdf: "[[2025-corrfill-reference-based-inpainting.pdf]]"
date_ingested: 2026-06-18
---

# CorrFill: Enhancing Faithfulness in Reference-based Inpainting with Correspondence Guidance in Diffusion Models

![[2025-corrfill-reference-based-inpainting-thumbnail.png]]

## Research gap

CorrFill is a training-free plug-in module for reference-based image inpainting that enhances faithfulness to reference images by guiding the diffusion process with correspondence constraints. The key insight is that reference-based inpainting requires explicit geometric correlation awareness between reference and target images—a capability that naive diffusion conditioning often lacks. CorrFill estimates image correspondences from self-attention scores at each denoising step, uses these correspondences as explicit constraints to guide generation, and progressively refines both correspondences and inpainting results through iterative cycles. The method operates without additional training, making it a general strategy applicable to multiple diffusion-based inpainting models. Experiments on RealEstate10K and MegaDepth benchmarks demonstrate significant performance improvements, with particular gains in preserving reference image geometry and avoiding spurious object insertion.

## Contributions

- **Correspondence-guided diffusion**: First to use explicit correspondence constraints as guidance for reference-based inpainting diffusion models
- **Training-free approach**: No additional learnable parameters or task-specific training required; works as plug-in module
- **Self-attention exploitation**: Leverages self-attention scores in diffusion UNet as correspondence approximations for damaged image regions
- **Cyclic refinement**: Progressive joint refinement of correspondences and inpainting results through bidirectional feedback
- **Generality**: Applicable to multiple baseline diffusion models, improving various state-of-the-art methods

## Method

**Correspondence-guided inpainting pipeline**:

1. **Input preparation**: Stitch reference and target images side-by-side as input to diffusion model
2. **Attention harvesting**: At each denoising step, extract self-attention scores from diffusion UNet's attention layers
3. **Correspondence estimation**: Aggregate attention scores across layers into matching map; compute correspondence field from matching map
4. **Guidance application**: Use estimated correspondences as constraints to guide next denoising step via:
   - Attention masking: mask self-attention to enforce correspondence constraints
   - Objective function: optimize latent input tensor to respect correspondence constraints
5. **Cyclic update**: Repeat steps 2-4 iteratively, where correspondences from current step guide next step

**Key technical components**:
- Self-attention score aggregation from multiple UNet layers
- Correspondence field computation from matching maps
- Dual guidance mechanism: both attention-level and latent-level constraints
- Handling of damaged regions where classical correspondence methods fail

## Datasets & evaluation

- **RealEstate10K dataset**: Significant improvements in faithfulness metrics
- **MegaDepth dataset**: Better preservation of reference geometry
- **Baseline improvements**: Enhances multiple state-of-the-art diffusion-based inpainting methods
- **Qualitative**: Reduces unwanted object insertion, preserves correct geometric relationships

## Limitations

- How does correspondence quality degrade with extreme damage or missing visual overlap?
- Can the approach handle multiple reference images or time-varying correspondences?
- What is the computational overhead of iterative correspondence refinement?

## Key takeaways

This work advances reference-based inpainting (e.g., LeftRefill, other Stable Diffusion variants) by introducing explicit correspondence guidance. While prior methods condition diffusion on reference images, they lack direct awareness of reference-target geometric relationships. CorrFill bridges this gap using self-attention as a correspondence proxy, building on recent insights about attention in diffusion models representing geometric relationships.
