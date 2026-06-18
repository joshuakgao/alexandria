---
title: "Depth Anything V2"
authors: [Lihe Yang, Bingyi Kang, Zilong Huang, Zhen Zhao, Xiaogang Xu, Jiashi Feng, Hengshuang Zhao]
year: 2024
venue: "NeurIPS"
tags: [depth-estimation, synthetic-datasets]
url: ""
pdf: "[[2024-depth-anything-v2.pdf]]"
date_ingested: 2026-06-18
---

# Depth Anything V2

![[2024-depth-anything-v2-thumbnail.png]]

## Research gap

Depth Anything V2 significantly advances monocular depth estimation by addressing the critical limitations of Depth Anything V1 through a principled approach to data. Rather than introducing architectural complexity, the paper reveals three fundamental insights: (1) replacing all labeled real images with synthetic images eliminates label noise and preserves fine-grained details at object boundaries and thin structures; (2) scaling the teacher model capacity trained exclusively on synthetic data improves robustness to challenging cases (transparent objects, reflections); (3) bridging synthetic-to-real distribution shift by pseudo-labeling large-scale unlabeled real images with the scaled teacher. The result is a family of efficient models (25M to 1.3B parameters) achieving state-of-the-art zero-shot depth estimation on multiple benchmarks—outperforming diffusion-based methods (Marigold) in both speed (10× faster) and accuracy while maintaining the robustness strengths of discriminative architectures. The paper also introduces a versatile evaluation benchmark addressing annotation noise in existing datasets.

## Contributions

- **Synthetic-to-real transfer via pseudo-labeling**: Demonstrates that replacing real labeled images with precise synthetic images removes label noise, enabling fine-grained detail preservation while large-scale pseudo-labeled real images prevent distribution shift and ensure scene diversity
- **Paradigm shift in data design**: First to show that efficient discriminative models (not diffusion-based generative models) can achieve fine-grained details when trained on high-quality synthetic data
- **Scaled teacher-student framework**: Scales teacher model capacity to improve robustness to transparent objects and reflections, then distills to efficient students via pseudo-labeled real images
- **Versatile model family**: Provides models from 25M to 1.3B parameters with fine-grained and metric depth variants, addressing diverse deployment scenarios
- **Clean evaluation benchmark**: Constructs new benchmark with precise annotations and diverse scenes to address noise in existing test sets (NYU-D, HRWSI, MegaDepth)

## Method

The approach consists of three key stages:

1. **Teacher on synthetic images**: Train large capacity teacher model (DINOv2-Giant backbone) exclusively on synthetic data (Hypersim, Virtual KITTI). Synthetic data provides: (a) precise depth labels without sensor/matching errors, (b) complete fine-grained supervision at boundaries and thin structures, (c) accurate depth for transparent/reflective surfaces.

2. **Pseudo-label real images**: Use large capacity teacher to annotate unlabeled real images. This addresses two synthetic data limitations: (a) distribution shift between synthetic and real images, (b) limited scene diversity. Crucially, the teacher's robustness (from synthetic training) produces high-quality pseudo-labels.

3. **Student models on pseudo-labels**: Train smaller student models (various scales) on large-scale pseudo-labeled real images, achieving both fine-grained details and real-world generalization.

Key design choices: uses DINOv2 encoders (not diffusion models) for efficiency; multi-scale synthetic dataset combinations; careful label quality filtering for pseudo-labeling.

## Datasets & evaluation

**Zero-shot depth estimation** (single model, no fine-tuning):
- Outperforms Depth Anything V1 on standard benchmarks (NYU, KITTI, ScanNet, etc.)
- Comparable or superior fine-grained details to Marigold (diffusion-based) while 10× faster
- Significantly improved robustness: 83.6% accuracy on Transparent Surface Challenge (vs. 53.5% for V1, 25.9% for MiDaS)
- Handles reflections, complex scenes, thin objects better than V1

**Efficiency**:
- Small model: 25M parameters, 60ms inference (V100)
- Large model: 335M parameters, 213ms inference
- DINOv2-based approach is dramatically faster than Stable Diffusion-based Marigold (2.1s)

**Transferability**:
- Metric depth fine-tuning maintains strong zero-shot generalization
- Serves as pre-training for downstream tasks

## Limitations

- How does performance scale with synthetic dataset size? Is there a saturation point?
- Can synthetic-to-real bridging via pseudo-labels generalize to other dense prediction tasks (optical flow, surface normals)?
- How sensitive are results to pseudo-label quality? What filtering strategies are most effective?
- Do fine-grained details from synthetic training transfer to significantly different real-world domains (medical imaging, underwater, thermal)?
- How does the approach perform on extreme cases (extreme weather, night vision, artistic/stylized content)?

## Key takeaways

**Depth Anything V1**: V2 maintains discriminative architecture strengths (efficiency, robustness to complex scenes) but dramatically improves fine-grained detail preservation by switching from real labeled images to synthetic data. V2 is a direct successor addressing V1's key limitation.

**Marigold & diffusion-based methods**: V2 achieves comparable fine-grained details without the computational overhead of diffusion models, demonstrating that the limiting factor was data quality, not architecture.

**Metric3D**: Concurrent work also exploring scaling and metric depth; V2 offers multiple efficiency tiers and demonstrates synthetic-to-real bridge as key insight.

**Prior synthetic data work**: First to show systematic advantage of synthetic-over-real training for depth at scale, connecting insights from self-training (V1) with synthetic data precision.
