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

*Last updated: 2026-06-25 | Sources: 3 papers*

## Current thesis

The field has evolved from purely supervised learning toward leveraging both unlabeled data and high-quality synthetic data. While self-training on massive unlabeled datasets (62M images) with strong perturbations achieves robustness and generalization, the critical bottleneck is **data quality**: precise synthetic images eliminate label noise and preserve fine-grained details, while pseudo-labeled real images bridge distribution shift and provide scene diversity. Pre-trained image editing models offer alternative geometric priors, but synthetic-to-real transfer via scaled teacher-student learning achieves comparable or superior results with dramatically higher efficiency (10× faster inference than diffusion models). A new axis is now emerging: **temporal consistency and real-time deployment**. FlashDepth (CVPR 2025) shows that building on strong single-image models (DAv2) with lightweight recurrent alignment (~1% added parameters) and a dual-resolution hybrid architecture achieves 24 FPS at 2K resolution — 11× faster than diffusion-based video depth methods — while maintaining competitive accuracy. The key insight across V1, V2, FE2E, and FlashDepth: architecture matters less than data and pre-training, and the path from single-image to video depth is shorter than expected when the single-image foundation is strong enough.

## Key trends

The field has evolved through increasingly sophisticated approaches to data leverage:

1. **Multi-dataset supervised learning (MiDaS, ~2019)**: Combining labeled datasets with affine-invariant losses to improve zero-shot generalization
2. **Unlabeled data at scale (Depth Anything V1, 2024)**: Self-training on 62M unlabeled images with strong perturbations outperforms purely labeled baselines; the key insight is that strong augmentations and semi-supervised learning unlock generalization
3. **Synthetic data precision (Depth Anything V2, 2024)**: Replaces real labeled images with synthetic data to eliminate label noise and preserve fine-grained details; bridges distribution shift using pseudo-labels from scaled teacher model, combining synthetic precision with real-world diversity. This is a dramatic paradigm shift: **data quality matters more than architecture**
4. **Generative priors (FE2E, 2026)**: Pre-trained image editing models provide alternative geometric priors; achieve state-of-the-art with minimal training data
5. **Unifying insight**: Across V1→V2→FE2E, the evolution shows that any source of high-quality supervision (synthetic data, large unlabeled data, or generative priors) can drive state-of-the-art performance if properly leveraged via scaled training and data engineering
6. **Real-time streaming video depth (FlashDepth, 2025)**: Rather than training expensive video diffusion models, FlashDepth adds a lightweight Mamba recurrent module to DAv2 for temporal consistency and uses a hybrid dual-resolution design (ViT-S at 2K for sharp boundaries, ViT-L at 518px for accuracy, fused via cross-attention). This achieves 24 FPS at 2K on a single A100 — demonstrating that the single-image-to-video gap can be bridged with minimal additional components when the base model is strong

## Open problems

- How much synthetic data is needed to saturate performance? Is there a data-efficiency frontier across architectures?
- Can synthetic-to-real pseudo-labeling via scaled teacher-student transfer to other dense tasks (optical flow, surface reconstruction, normal estimation)?
- What are the failure modes of pseudo-labeled data? How sensitive are results to label quality and filtering strategies?
- Can these approaches handle extreme domain shifts (medical imaging, thermal, artistic content, extreme weather)?
- How do scaling laws differ between synthetic-only, real-only, and synthetic+pseudo-labeled pipelines?
- What is the theoretical explanation for why synthetic precision outweighs unlabeled data diversity, and when might this reversal?
- Can lightweight recurrent consistency (FlashDepth's Mamba approach) generalize to other dense prediction tasks (optical flow, normals) or does temporal alignment require task-specific design?
- How does FlashDepth's affine-invariant depth interact with downstream tasks requiring metric depth (robotics, AR)? Can metric scale be recovered cheaply from sparse signals (IMU, single LiDAR point)?
- What is the upper bound on sequence length before recurrent hidden state drift degrades consistency?

## Contradictions and debates

- **Diffusion vs. recurrent for video consistency**: Diffusion-based methods (DepthCrafter) achieve consistency through strong motion priors but are slow and require batch processing. FlashDepth shows a lightweight recurrent module achieves competitive consistency at 11× the speed. Whether diffusion priors provide fundamentally better consistency on challenging sequences (large occlusions, scene cuts) or whether the speed-accuracy tradeoff always favors recurrent approaches is unresolved.
- **Resolution vs. accuracy**: FlashDepth shows numerical accuracy is resolution-independent (<1% of pixels are boundaries), challenging the assumption that higher resolution always improves depth quality. However, boundary sharpness — critical for applications like video editing and object insertion — does require high resolution.

## Recommended reading order

1. Start with [[papers/2024-depth-anything|Depth Anything V1]] to understand the self-training paradigm and why strong perturbations are critical for leveraging unlabeled data
2. Read [[papers/2024-depth-anything-v2|Depth Anything V2]] to see the paradigm shift: precise synthetic data + pseudo-labeled real images outperforms V1's large unlabeled approach and matches diffusion models while being 10× faster
3. Compare with [[papers/2026-fe2e-from-editor-to-dense-geometry-estimator|FE2E]] to see an alternative path via generative priors
4. Read [[papers/2025/2025-flashdepth/2025-flashdepth|FlashDepth]] to see how strong single-image models extend to real-time streaming video depth with minimal added components
5. Explore concept pages ([[concepts/self-training|Self-Training]], [[concepts/pseudo-labeling|Pseudo-Labeling]], [[concepts/dense-prediction|Dense Prediction]]) to understand key techniques and their nuances

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
