---
title: "OneDiff: A Generalist Model for Image Difference Captioning"
authors: [Erdong Hu, Longteng Guo, Tongtian Yue, Zijia Zhao, Shuning Xue, Jing Liu]
year: 2025
tags: [change-detection, vision-language-models, benchmarking]
url: ""
pdf: "[[2025-onediff.pdf]]"
date_ingested: 2026-06-18
---

# OneDiff: A Generalist Model for Image Difference Captioning

![[2025-onediff-thumbnail.png]]

## Research gap

OneDiff is a generalist vision-language model for Image Difference Captioning (IDC) — the task of generating natural language descriptions of changes between image pairs. Unlike prior specialist IDC models tailored to specific benchmarks (surveillance, synthetic scenes, bird species), OneDiff uses a unified architecture that generalizes across diverse IDC scenarios without benchmark-specific tuning.

## Contributions

- A generalist IDC model architecture with a Visual Delta Module that uses learnable delta tokens to probe multi-level encoder features of image pairs via cross-attention, capturing fine-grained visual differences
- Coupled Sample Training (CST) strategy that creates pseudo dual-image training samples from single image-text pairs, enabling efficient pretraining for multi-image understanding
- DiffCap dataset (363K entries) combining task-specific benchmarks, retrieved real image pairs with GPT-assisted captions, and synthetic pairs from InstructPix2Pix
- State-of-the-art performance across three diverse IDC benchmarks without benchmark-specific adaptation (up to 211% CIDEr improvement on Image-Editing-Request)

## Method

**Architecture:** Siamese CLIP-ViT-L/14 encodes both images independently. The Visual Delta Module (inherited from BERT-base, 6 layers) takes learnable delta tokens as queries that interact with image hidden states extracted at layers 8, 16, and 24 of the vision encoder through alternating self-attention and cross-attention. A 2-layer MLP projects visual features (576 patches per 336×336 image, 2048→4096 dim) into the LLM space. Vicuna-7B generates the difference caption.

**Stage I — CST Pretraining:** Dynamically merges two image-text pairs into pseudo dual-image samples (concatenated images as input, concatenated captions as output). Only VDM and MLP are trained; vision encoder and LLM frozen.

**Stage II — Multi-task SFT:** Instruction tuning on DiffCap (335K IDC pairs) mixed with LLaVA-665K for 1M total pairs. Full fine-tuning of all parameters including LLM. Task-specific prompts guide training across different IDC scenarios.

## Datasets & evaluation

- **Image-Editing-Request:** 109.6 CIDEr (vs. 35.7 prior SOTA — 211% improvement), 29.6 BLEU-4, 25.1 METEOR
- **Spot-the-Diff:** 56.6 CIDEr (vs. 47.4 prior SOTA), 12.8 BLEU-4, 14.6 METEOR
- **Birds-to-Words:** 28.4 CIDEr (vs. 25.0 prior SOTA), 27.1 BLEU-4, 25.8 METEOR
- Ablation shows CST and VDM are synergistic — both are needed for best performance; either alone provides modest gains
- Both real and synthetic DiffCap data contribute complementary improvements

## Limitations

- The model operates on image pairs only — extension to video-based or multi-temporal change captioning is unexplored
- Ground truth quality depends on GPT-generated captions for the synthetic and retrieved portions of DiffCap, inheriting potential hallucination artifacts
- The model does not localize changes spatially (no bounding boxes or segmentation masks) — it only describes them in text
- Evaluation is limited to standard captioning metrics (BLEU, CIDEr, METEOR) which may not fully capture semantic correctness of change descriptions
- Scaling to higher-resolution images or more complex multi-object change scenarios needs investigation

## Key takeaways

The architecture combines a siamese CLIP-ViT-L/14 image encoder with a novel Visual Delta Module (VDM) — a set of learnable delta tokens that interact with multi-level encoder features from both images via alternating self-attention and cross-attention layers, capturing fine-grained differences at multiple feature scales. These delta tokens, along with projected image features, are fed to a Vicuna-7B LLM for caption generation.

Training follows a two-phase strategy: (1) Coupled Sample Training (CST), which creates pseudo dual-image samples by dynamically pairing single image-text pairs from LLaVA-558K data for cross-modal alignment pretraining; and (2) multi-task instruction tuning on DiffCap, a new 363K-entry dataset combining task-specific IDC benchmarks, retrieved real image pairs with GPT-generated difference captions, and synthetic image pairs from InstructPix2Pix with GPT-3 summaries. OneDiff achieves up to 97% CIDEr improvement over prior SOTA across Spot-the-Diff, Image-Editing-Request, and Birds-to-Words benchmarks.

Prior IDC methods (CLIP4IDC, NCT, VARD, SCORER) are specialist models using task-specific attention mechanisms that lack cross-benchmark generalization. OneDiff bridges IDC with the broader VLM paradigm, adapting LLaVA-style architecture for dual-image input. The Visual Delta Module draws inspiration from Q-Former (BLIP-2) and Flamingo's cross-attention design but is specialized for capturing inter-image differences rather than single-image understanding. The CST strategy addresses the fundamental data scarcity problem in paired-image understanding by bootstrapping from single-image VLM pretraining data. This work connects to the broader change detection literature by providing a caption-level description of changes, complementing pixel-level and semantic segmentation approaches.
