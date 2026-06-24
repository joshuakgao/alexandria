---
title: "GenRecon: Bridging Generative Priors for Multi-View 3D Scene Reconstruction"
authors: [Katharina Schmid, Fynn von Lützow, Jiri Hladký, Angela Dai, Matthias Nießner]
year: 2026
venue: ""
tags: [3d-scene-reconstruction, diffusion-models]
url: ""
pdf: "[[2026-genrecon.pdf]]"
date_ingested: 2026-06-18
---

# GenRecon: Bridging Generative Priors for Multi-View 3D Scene Reconstruction

![[2026-genrecon-thumbnail.png]]

## Research gap

GenRecon introduces a new approach to high-fidelity 3D scene reconstruction from sparse multi-view RGB images by tightly coupling reconstruction with a strong generative 3D prior. Rather than treating reconstruction as a deterministic regression problem, GenRecon formulates it as conditional 3D generation over spatially-localized, overlapping chunks that tile the scene. This enables the method to inherit the fidelity, completeness, and PBR material quality of state-of-the-art object-level generative models (Trellis.2), extending them to scene scale.

## Contributions

- Formulation of scene reconstruction as conditional 3D generation over overlapping spatial chunks, scaling an object-level generative prior (Trellis.2) to scene level
- Projection-based 3D conditioning pathway that lifts multi-view image features into spatially-grounded, view-order-invariant 3D representations for pose-controlled generation
- Joint chunk generation via MultiDiffusion-style averaging in overlap regions, enforcing cross-chunk consistency during the flow-matching trajectory
- Parameter-efficient adaptation (LoRA fine-tuning) that preserves the pretrained generative prior while enabling scene-level generation
- Scene-level PBR mesh output with material properties (albedo, metallic, roughness) enabling relighting and editing in standard rendering pipelines

## Method

**Scene Calibration & Chunking**: COLMAP recovers camera poses and sparse points from unposed input images. The scene volume is partitioned into fixed-size overlapping chunks with prescribed minimum overlap margin.

**Global 3D Conditioning**: Each input image is encoded with DINOv3 into dense 2D feature maps. Per-view features are lifted into a scene-sized 3D voxel grid by projecting each voxel into each view's image plane and sampling the corresponding feature. Per-view grids are aggregated via IBRNet-style permutation-invariant fusion: cross-view mean/variance statistics provide global context; per-voxel features are refined and weighted by two small MLPs; the final feature is mean + softmax-weighted residual (zero-initialized for identity at init). Per-chunk conditions are cropped from this global grid.

**Joint Chunk Generation**: Trellis.2's flow-matching denoiser generates all chunks jointly from a global noisy latent. At each step, per-chunk predictions are merged by averaging in overlap regions. A boundary-sensitive variant excludes chunk-boundary voxels from contributing (but not receiving) to improve seam coherence. The generation proceeds through three stages: coarse occupancy → high-resolution shape → PBR texture, each with its own conditioning resolution.

**Training**: Conditioning pathway + LoRA adapter trained on synthetic scene data (SAGE-10k + 3D-FRONT subset); remaining Trellis.2 weights frozen.

## Datasets & evaluation

On ScanNet++ (real-world, 25 scenes, 8 views): Chamfer 0.0688m (vs. 0.0819 FineRecon), F-score@10cm 0.7771, normal consistency 0.7860, LPIPS 0.0872 (best by large margin), completeness 0.9904. On 3D-FRONT (synthetic): Chamfer 0.0638m (vs. 0.1584 Murre), F-score@10cm 0.8655. Ablations confirm the 3D conditioning pathway is essential for pose-correct generation (without it, geometry is plausible but misaligned). Performance scales with number of input views (1→8 images: Chamfer 0.0812→0.0291 on SAGE-10k). Trained only on synthetic data but generalizes to unseen real-world scenes.

## Limitations

- Non-Lambertian surfaces (glass, mirrors) are unreliable due to underrepresentation in training data
- Chunk partitioning assumes indoor scenes with vertical extent ≤~5m; extending to outdoor or multi-story scenes requires adaptive chunking
- The strong generative prior may hallucinate content in weakly-observed regions — the completeness gain trades off against occasional inaccuracies
- Requires COLMAP for initial pose estimation, inheriting its failure modes (textureless scenes, repetitive patterns)
- PBR material quality is limited by SAGE-10k's VLM-predicted materials rather than ground-truth measured SVBRDF
- Runtime likely significantly slower than feed-forward methods (π³, DAGE) due to iterative flow-matching denoising over multiple chunks

## Key takeaways

The key technical contribution is a projection-based 3D conditioning pathway that lifts multi-view DINOv3 image features into per-view 3D voxel grids, then aggregates them across views in a permutation-invariant manner (IBRNet-style mean + softmax-weighted residual). This spatially-grounded conditioning is injected into the Trellis.2 flow-matching denoiser via zero-initialized residual connections, ensuring pose control and multi-view consistency emerge directly from the design. At test time, all chunks are generated jointly via a MultiDiffusion-style scheme that averages predictions in overlap regions, enforcing cross-chunk consistency throughout the generation trajectory.

GenRecon outperforms five baseline methods (2DGS, MonoSDF, DA3, FineRecon, Murre) on both ScanNet++ (real-world) and 3D-FRONT (synthetic), achieving the best Chamfer distance (0.0638 on 3D-FRONT, 0.0688 on ScanNet++), highest F-score, and best perceptual metrics — a ~16% improvement over the next-best method. The output is a complete PBR mesh with albedo, metallic, and roughness channels, directly importable into standard rendering engines for relighting and editing.

GenRecon represents a paradigm shift within the 3d-scene-reconstruction topic. Where π³ and DAGE are feed-forward regression models (deterministic, single-pass), GenRecon is a conditional generative model that samples from a learned distribution. This gives it the ability to hallucinate plausible geometry in unobserved regions — a capability no regression-based method can match — at the cost of potential hallucination artifacts.

The chunked generation approach with MultiDiffusion-style averaging is analogous to DAGE's dual-stream concern separation, but operates in latent space rather than feature space. The projection-based conditioning pathway shares the spatial grounding philosophy of π³'s permutation-equivariant design, but achieves view invariance through aggregation rather than architectural symmetry.

GenRecon's PBR mesh output distinguishes it from all other methods in this topic, which produce point clouds, depth maps, or Gaussian splats. This makes it uniquely suited for content creation, simulation, and embodied AI applications requiring editable, relightable assets.
