---
topic: Denoising Segmentations
slug: denoising-segmentations
---

# Denoising Segmentations

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "denoising-segmentations")
SORT year DESC
```

## Overview

*Last updated: 2026-04-07 | Sources: 2 papers*

## Current thesis

Segmentation can be improved through two complementary approaches: (1) denoising diffusion models can learn and enforce mask priors (geometric and semantic constraints) to refine base segmentor predictions, achieving substantial performance gains; (2) robustness to real-world annotation noise remains a critical challenge, as current learning-from-noise techniques fail to handle spatial inaccuracies unique to dense prediction. Together, these findings motivate both better prior modeling during inference and better handling of imperfect training annotations.

## Key trends

Two distinct but complementary research directions are emerging:

**Generative priors for segmentation (2023–2024)**:
- [[2023-denoising-diffusion-semantic-segmentation|Denoising diffusion approaches]] show that learning mask priors p(x) via generative models can effectively refine discriminative base predictions
- Discrete diffusion proves more suitable for masks than continuous formulations
- Mask priors capture geometric and semantic constraints that base models miss

**Robustness to annotation noise (2025)**:
- [[2025-noisy-annotations-segmentation|Systematic characterization]] of real-world annotation errors and their impact across datasets
- Benchmark construction (COCO-N, CityScapes-N, VIPER-N, COCO-WAN) enables reproducible evaluation
- Gap identification: standard LNL methods fail for spatial inaccuracies

## Open problems

- How can segmentation models be made inherently robust to annotation noise without requiring synthetic benchmarks or noise labels?
- What noise-aware training strategies (loss functions, regularization, architecture adaptations) are most effective for dense prediction?
- Do noise robustness properties transfer across datasets and domains?
- Can foundation models (SAM, CLIP) be used to automatically correct or denoise annotations?
- How should clinical and safety-critical applications (medical imaging, autonomous driving) handle annotation quality?

## Contradictions and debates

*No contradictions yet—field is emerging.*

## Recommended reading order

1. Start with [[papers/2023-denoising-diffusion-semantic-segmentation|Denoising Diffusion Semantic Segmentation]] to understand the mask prior perspective and how generative models can improve segmentation quality
2. Then read [[papers/2025-noisy-annotations-segmentation|Noisy Annotations in Semantic Segmentation]] to see the complementary challenge of robustness to imperfect training data
3. Explore concept pages ([[concepts/diffusion-models|Diffusion Models]], [[concepts/label-noise|Label Noise]], [[concepts/learning-from-noisy-labels|Learning from Noisy Labels]]) for deeper context

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
