---
title: "Denoising Diffusion Semantic Segmentation with Mask Prior Modeling"
authors: [Zeqiang Lai, Yuchen Duan, Jifeng Dai, Ziheng Li, Ying Fu, Hongsheng Li, Yu Qiao, Wenhai Wang]
year: 2023
tags: [denoising-segmentations, segmentation, diffusion-models]
url: ""
pdf: "[[2023-denoising-diffusion-semantic-segmentation.pdf]]"
date_ingested: 2026-06-18
---

# Denoising Diffusion Semantic Segmentation with Mask Prior Modeling

![[2023-denoising-diffusion-semantic-segmentation-thumbnail.png]]

## Research gap

This paper proposes DDPS (Denoising Diffusion Prior for Segmentation), a novel approach that complements traditional discriminative segmentation models with a generative mask prior learned via denoising diffusion models. Rather than solely maximizing p(x|y) (image features to pixels), DDPS models the intrinsic prior distribution p(x) of segmentation masks themselves, capturing geometric and semantic constraints like object shape, co-occurrence patterns, and topological regularities. The method employs discrete diffusion with a unified architecture comprising: (1) a mask representation codec, (2) a base segmentation model providing initial predictions, and (3) a diffusion prior that iteratively refines predictions. Experiments on ADE20K and Cityscapes demonstrate substantial improvements (e.g., Segformer-B2 from 46.80% to 49.73% mIoU) with notably better visual quality.

## Contributions

- **Novel paradigm**: Complementing discriminative segmentation with a generative mask prior modeling approach using denoising diffusion
- **Discrete diffusion for segmentation**: First systematic exploration of discrete diffusion (vs. continuous) for dense prediction tasks
- **Key design insights**: (1) careful integration of diffusion is critical—poor design degrades performance; (2) what is denoised (mask object) matters more than noise type; (3) inference can relax strict diffusion schemes to simpler alternatives
- **Empirical gains**: Substantial improvements on standard benchmarks with improved visual coherence and geometric regularity

## Method

DDPS combines a base discriminative segmentor with a discrete diffusion prior:
- **Base segmentor**: Produces initial mask prediction x₀ from image features (maximizing p(x|y))
- **Mask codec**: Transforms masks into discrete representation amenable to diffusion
- **Diffusion prior**: Iteratively refines x₀ by denoising toward the learned mask prior p(x), incorporating geometric and semantic constraints
- **Discrete diffusion process**: Uses bit-level or categorical diffusion rather than continuous Gaussian noise
- **Training**: Adds noise according to q(xₜ|x₀), with key finding that object identity matters more than noise characteristics
- **Inference**: Can use simplified diffusion scheme q(xₜ₋₁|x₀) rather than strict q(xₜ₋₁|xₜ, x₀)

## Datasets & evaluation

- **ADE20K**: Segformer-B2 improved from 46.80% to 49.73% mIoU (2.93 point gain)
- **Cityscapes**: Competitive mIoU with superior visual quality
- **Qualitative**: Improved geometric regularity, filled holes, removed artifacts, better boundary coherence
- **Generality**: Works with multiple segmentation architectures (Segformer, DeepLabV3)

## Limitations

- How do mask priors generalize to novel object categories or datasets not seen during training?
- Can discrete diffusion scales effectively to very high-resolution segmentation tasks?
- How sensitive is performance to the choice of mask codec representation?
- Can this approach efficiently integrate with real-time segmentation systems?

## Key takeaways

This work bridges generative and discriminative paradigms in segmentation. While GANs were previously used for joint distribution modeling p(x, y), they suffered from training instability and competitive performance. This work leverages recent advances in diffusion models, which have proven more stable and effective. Unlike concurrent continuous diffusion approaches for segmentation, DDPS explores discrete diffusion and identifies practical design choices specific to mask prior modeling.
