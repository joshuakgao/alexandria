---
topic: Image Generation
slug: image-generation
---

# Image Generation

## Papers

| Year | Thumbnail                         | Paper               | Venue |
| ---- | --------------------------------- | ------------------- | ----- |
| 2026 | ![[2026-gpic-thumbnail.png\|400]] | [[2026-gpic\|GPIC]] |       |

## Overview

Image generation encompasses methods for synthesizing novel images, including GANs, diffusion models, flow matching, and autoregressive approaches. This topic covers architectures, training strategies, conditioning mechanisms (class labels, text prompts), and evaluation protocols. Modern text-to-image generation relies on large-scale captioned datasets and increasingly operates in pixel space or learned latent spaces.

## Trends

- Pixel-space flow matching models (e.g., JiT) emerging as simple, single-stage alternatives to latent diffusion.
- FID on ImageNet-1K is saturated — several models now beat the real-image oracle — driving adoption of FD-DINOv2 as a replacement metric.
- Text-conditioned generation has largely replaced class-conditional generation as the dominant paradigm.
- Growing need for standardized, reproducible benchmarks beyond ImageNet-1K.

## Open questions

- What evaluation metrics best capture perceptual quality and diversity without being gameable?
- How to scale generative models to higher resolutions efficiently without multi-stage pipelines?
- What is the relationship between dataset scale, diversity, and generative model quality?
- How to prevent memorization of training data in models trained on internet-scale corpora?
