---
topic: Depth Estimation
slug: depth-estimation
---

# Depth Estimation

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "depth-estimation")
SORT year DESC
```

## Overview

*Last updated: 2026-04-10 | Sources: 2 papers*

## Current thesis

The field has evolved from purely supervised learning toward leveraging both unlabeled data and high-quality synthetic data. While self-training on massive unlabeled datasets (62M images) with strong perturbations achieves robustness and generalization, the critical bottleneck is **data quality**: precise synthetic images eliminate label noise and preserve fine-grained details, while pseudo-labeled real images bridge distribution shift and provide scene diversity. Pre-trained image editing models offer alternative geometric priors, but synthetic-to-real transfer via scaled teacher-student learning achieves comparable or superior results with dramatically higher efficiency (10× faster inference than diffusion models). The key insight across V1, V2, and FE2E: architecture matters less than data—whether synthetic precision or generative modeling—and how to leverage unlabeled/pseudo-labeled data to scale learning.

## Key trends

The field has evolved through increasingly sophisticated approaches to data leverage:

1. **Multi-dataset supervised learning (MiDaS, ~2019)**: Combining labeled datasets with affine-invariant losses to improve zero-shot generalization
2. **Unlabeled data at scale (Depth Anything V1, 2024)**: Self-training on 62M unlabeled images with strong perturbations outperforms purely labeled baselines; the key insight is that strong augmentations and semi-supervised learning unlock generalization
3. **Synthetic data precision (Depth Anything V2, 2024)**: Replaces real labeled images with synthetic data to eliminate label noise and preserve fine-grained details; bridges distribution shift using pseudo-labels from scaled teacher model, combining synthetic precision with real-world diversity. This is a dramatic paradigm shift: **data quality matters more than architecture**
4. **Generative priors (FE2E, 2026)**: Pre-trained image editing models provide alternative geometric priors; achieve state-of-the-art with minimal training data
5. **Unifying insight**: Across V1→V2→FE2E, the evolution shows that any source of high-quality supervision (synthetic data, large unlabeled data, or generative priors) can drive state-of-the-art performance if properly leveraged via scaled training and data engineering

## Open problems

- How much synthetic data is needed to saturate performance? Is there a data-efficiency frontier across architectures?
- Can synthetic-to-real pseudo-labeling via scaled teacher-student transfer to other dense tasks (optical flow, surface reconstruction, normal estimation)?
- What are the failure modes of pseudo-labeled data? How sensitive are results to label quality and filtering strategies?
- Can these approaches handle extreme domain shifts (medical imaging, thermal, artistic content, extreme weather)?
- How do scaling laws differ between synthetic-only, real-only, and synthetic+pseudo-labeled pipelines?
- What is the theoretical explanation for why synthetic precision outweighs unlabeled data diversity, and when might this reversal?

## Contradictions and debates

*No contradictions yet—early in topic development.*

## Recommended reading order

1. Start with [[papers/2024-depth-anything|Depth Anything V1]] to understand the self-training paradigm and why strong perturbations are critical for leveraging unlabeled data
2. Read [[papers/2024-depth-anything-v2|Depth Anything V2]] to see the paradigm shift: precise synthetic data + pseudo-labeled real images outperforms V1's large unlabeled approach and matches diffusion models while being 10× faster
3. Compare with [[papers/2026-fe2e-from-editor-to-dense-geometry-estimator|FE2E]] to see an alternative path via generative priors
4. Explore concept pages ([[concepts/self-training|Self-Training]], [[concepts/pseudo-labeling|Pseudo-Labeling]], [[concepts/dense-prediction|Dense Prediction]]) to understand key techniques and their nuances

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
