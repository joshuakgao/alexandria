---
title: "Hypersim: A Photorealistic Synthetic Dataset for Holistic Indoor Scene Understanding"
authors: [Mike Roberts, Jason Ramapuram, Anurag Ranjan, Atulit Kumar, Miguel Angel Bautista, Nathan Paczan, Russ Webb, Joshua Susskind]
year: 2021
venue: "ICCV 2021"
tags: [synthetic-datasets, 3d-scene-understanding]
url: ""
pdf: "[[2021-hypersim-photorealistic-synthetic-dataset-indoor-scene.pdf]]"
date_ingested: 2026-06-18
---

# Hypersim: A Photorealistic Synthetic Dataset for Holistic Indoor Scene Understanding

![[2021-hypersim-photorealistic-synthetic-dataset-indoor-scene-thumbnail.png]]

## Research gap

Hypersim is a large-scale photorealistic synthetic dataset for indoor scene understanding, addressing the challenge that per-pixel ground truth labels are difficult to obtain from real images. The dataset comprises 77,400 images from 461 professionally-created indoor scenes, with comprehensive per-pixel annotations and geometry. Key features: (1) uses only publicly available 3D assets; (2) provides complete scene geometry, materials, and lighting; (3) includes dense semantic+instance segmentations and full camera information; (4) factors images into disentangled components (diffuse reflectance, diffuse illumination, non-diffuse residual for view-dependent effects). The entire dataset was generated for approximately half the cost of training a popular large NLP model. Sim-to-real transfer experiments demonstrate significant improvements on semantic segmentation and 3D shape prediction tasks, with state-of-the-art results on the Pix3D benchmark.

## Contributions

- **Comprehensive synthetic dataset**: 77,400 images from 461 scenes with dense per-pixel labels and complete 3D geometry
- **Public 3D assets**: First major dataset to rely exclusively on publicly available assets, enabling reproducibility and future extensions
- **Holistic annotation**: Complete geometry, materials, lighting, and semantic/instance segmentations for every image
- **Image factorization**: Novel decomposition of images into diffuse reflectance, diffuse illumination, and non-diffuse residuals—enabling both rendering research and inverse rendering tasks
- **Cost efficiency**: Demonstrates that high-quality synthetic data generation is economically viable
- **Sim-to-real validation**: Strong transfer learning results on real-world semantic segmentation and 3D shape prediction tasks

## Method

Dataset generation pipeline:

**Scene sourcing**:
- 461 indoor scenes downloaded from professional 3D asset marketplace
- All assets are publicly available (no proprietary or restricted content)

**Rendering**:
- Generate 77,400 diverse viewpoints per scene (multiple camera positions/orientations)
- Render at high quality using physically-based rendering

**Per-pixel annotation**:
- **Geometry**: Depth, surface normals, camera intrinsics/extrinsics
- **Semantics**: Per-pixel semantic class labels and instance segmentations
- **Photometric**: Factorized into three components:
  - Diffuse reflectance (material properties)
  - Diffuse illumination (incoming light)
  - Non-diffuse residual (specular highlights, glossy reflections, view-dependent effects)
- All stored as HDR images; components can be recomposed to reconstruct original color image

**Quality control**:
- Extensive analysis at scene, object, and pixel levels
- Cost analysis in terms of money, computation, and annotation effort

## Datasets & evaluation

**Sim-to-real transfer**:
- **Semantic Segmentation**: Pre-training on Hypersim significantly improves performance on real datasets
- **3D Shape Prediction**: State-of-the-art on Pix3D benchmark, challenging real-world dataset
- Demonstrates that synthetic data generalizes well despite domain gap

**Cost analysis**:
- Entire dataset generation: ~half the cost of training popular large NLP models
- Computationally and economically feasible alternative to manual annotation

**Dataset characteristics**:
- 77,400 images, 461 scenes
- Complete geometry and material information
- Dense segmentations (semantic + instance)
- Comprehensive camera and lighting information

## Limitations

- How well does the sim-to-real transfer generalize to outdoor scenes or non-indoor environments?
- Can the image factorization (diffuse/non-diffuse) be improved to better capture complex material properties (e.g., subsurface scattering)?
- How does performance scale when fine-tuning on real data, rather than pre-training only?
- Can the synthetic data be extended to dynamic scenes or video sequences?
- How sensitive are results to photorealism details vs. synthetic-ness indicators?

## Key takeaways

Hypersim addresses limitations of prior synthetic datasets (SceneNet-RGBD, InteriorNet, Replica, etc.) by being the first to combine: (1) publicly available 3D assets, (2) dense semantic+instance segmentations, (3) complete geometry/material/lighting information, and (4) disentangled image factorization. This comprehensive approach enables broader applications in multi-task learning, geometric estimation, and inverse rendering compared to single-task datasets.
