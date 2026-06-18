---
title: "Changen2: Multi-Temporal Remote Sensing Generative Change Foundation Model"
authors: [Zhuo Zheng, Stefano Ermon, Dongjun Kim, Liangpei Zhang, Yanfei Zhong]
year: 2024
tags: [change-detection, synthetic-datasets, diffusion-models, remote-sensing]
url: ""
pdf: "[[2024-changen2.pdf]]"
date_ingested: 2026-06-18
---

# Changen2: Multi-Temporal Remote Sensing Generative Change Foundation Model

![[2024-changen2-thumbnail.png]]

## Research gap

Changen2 addresses the data scarcity challenge in remote sensing change detection by introducing a generative change foundation model that synthesizes realistic multi-temporal image sequences and dense change labels from single-temporal images. The core innovation is framing the stochastic change process as a probabilistic graphical model (GPCM) that factorizes into two tractable subproblems: condition-level change event simulation (adding/removing/editing objects at the semantic mask level) and image-level semantic change synthesis (generating post-event images via diffusion). Changen2 is implemented with a resolution-scalable diffusion transformer (RS-DiT) that can generate arbitrary-resolution time series without training at high resolution. Crucially, the model can be trained on unlabeled data using self-supervision via SAM-generated object contours, enabling large-scale pretraining. Pre-trained models achieve near-supervised performance in zero-shot settings (narrowing gaps to 3% on LEVIR-CD) and demonstrate superior transferability across diverse change tasks including building change, LULC change, and disaster assessment.

## Contributions

- **Generative Probabilistic Change Model (GPCM):** Factorizes complex temporal dynamics into change event simulation (semantic transitions) and semantic change synthesis (image generation), making the problem tractable via deep generative modeling
- **Resolution-Scalable Diffusion Transformer (RS-DiT):** Removes absolute positional embeddings and uses local window attention to enable training on low-resolution images (256²) while generating arbitrary resolutions (1024²); adds 3×3 depthwise convolution in FFN for relative position information
- **Self-Supervised Pretraining at Scale:** Trains on massive unlabeled earth observation data by using SAM-generated object contours as editable conditions; produces multi-temporal change-labeled datasets without manual annotation
- **Three Large-Scale Synthetic Datasets:** Generates Changen2-S1-15k (building changes, 2 types), Changen2-S9-27k (LULC changes, 38 types), and Changen2-S0-1.2M (class-agnostic, 1.2M pairs); first remote sensing foundation model with zero-shot change detection capability

## Method

**Generative Probabilistic Change Model Framework:**
The stochastic change process is described as P(S_{t+1}, S_t, I_{t+1}, I_t), factorized as:
P(S_cp) = P(I_{t+1}|S_{t+1}, I_t) · P(S_{t+1}|S_t) · P(I_t|S_t) · P(S_t)

This decouples into:
- **Change event simulation:** P(S_{t+1}|S_t) via rule-based functions (object creation F_c, removal F_r, attribute editing F_e)
- **Semantic change synthesis:** P(I_{t+1}|S_{t+1}, I_t) via diffusion model

**RS-DiT Architecture:**
- Based on Diffusion Transformer (DiT) with two key modifications: (1) remove absolute positional embeddings and add 3×3 depthwise convolution for relative position information, (2) replace global self-attention with local window attention to reduce quadratic complexity
- Dense embedding network (8 conv-layernorm-SiLU blocks with 2× downsampling) encodes semantic masks
- Latent diffusion model trained on VAE-encoded images with noise prediction objective

**Masked Change Diffusion Inference:**
Given pre-event image I_t and semantic mask S_t:
1. Sample post-event mask S_{t+1} via change event simulation
2. Compute change mask C = I(S_t ≠ S_{t+1})
3. Iteratively denoise with temporal mixing: x_{t+1}^{(i)} ← C ⊙ x_{t+1}^{(i)} + (1-C) ⊙ x_t^{(i)}, preserving unchanged regions

**Self-Supervised Learning:**
Uses SAM to extract object contours φ from unlabeled images as editable conditions. Trains on object removal: the change mask becomes a binary map of removed objects, enabling change supervision from unlabeled data at scale.

## Datasets & evaluation

**Zero-Shot Performance (no fine-tuning):**
- LEVIR-CD: 89% F1 (vs. 92% supervised) — 3% gap
- S2Looking: ~90% F1 (vs. 100% supervised) — 10% gap
- SECOND: ~92% F1 (vs. 100% supervised) — 10% gap
- Outperforms AnyChange (prior SOTA zero-shot) by 16.4% F1 at pixel level on SECOND

**Transferability:**
- Pre-trained Changen2-S0-1.2M foundation model outperforms seven state-of-the-art self-supervised remote sensing foundation models (SatMAE, SeCo, etc.)
- First remote sensing foundation model to bridge performance gap with supervised foundation models (SAM, Satlas)
- Demonstrates superior transferability across building change, LULC change, and disaster assessment tasks

**Synthetic Data Quality:**
- Temporal diversity of synthetic change data is key to transferability after pre-training
- Multi-class change generation (38 types) enables diverse change distribution
- Resolution scalability allows training efficient models on low-res images while generating diverse scales for pre-training

## Limitations

- **Temporal realism of synthetic changes:** While Changen2 generates realistic individual frames and change events, the long-term temporal consistency over long sequences (10+ timesteps) is not extensively validated; may not capture compound multi-year changes
- **Change event distribution:** Uniform sampling of change types may not match real-world change frequencies (e.g., sparse vegetation-to-water transitions vs. common urbanization); semantic transition matrix could be learned from real data
- **Pre-event image guidance balance:** The guidance ratio λ ∈ [0,1] is a hyperparameter; unclear how sensitive results are or how to select it automatically for different change types
- **Cross-domain generalization:** Datasets are sourced from globally distributed regions but within specific remote sensing domains (optical RGB); generalization to SAR, hyperspectral, or non-Earth observation domains is unexplored
- **Change mask quality:** Self-supervised approach uses binary object removal masks; more nuanced change annotations (partial damage, degradation) may require additional supervision

## Key takeaways

Extends [[2024-towards-generalizable-scene-change-detection|GeSCF (zero-shot scene change)]] by:
- Moving from zero-shot adaptation of pretrained models to **pre-training on synthetic change data**, enabling foundation models with inherent change understanding
- Addressing **multi-temporal** (arbitrary-length sequences) vs. bitemporal change detection
- Enabling **self-supervised learning** at massive scale, avoiding annotation bottlenecks that GeSCF relies on
- Generating **diverse change types** (38 types in LULC dataset) vs. single-category approaches

Complements [[2025-dynamicearth-open-vocabulary-change-detection|DynamicEarth (open-vocabulary change)]] by:
- Providing a **generative foundation model** that can be fine-tuned for semantic understanding tasks
- Pre-training strategy that establishes temporal understanding before VLM adaptation
