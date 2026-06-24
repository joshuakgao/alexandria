---
title: "SegFormer: Simple and Efficient Design for Semantic Segmentation with Transformers"
authors: [Enze Xie, Wenhai Wang, Zhiding Yu, Anima Anandkumar, Jose Alvarez, Ping Luo]
year: 2021
venue: "NeurIPS 2021"
tags: [segmentation]
url: "https://arxiv.org/abs/2105.15203"
pdf: "[[2021-segformer.pdf]]"
date_ingested: 2026-06-18
---

# SegFormer: Simple and Efficient Design for Semantic Segmentation with Transformers

![[2021-segformer-thumbnail.png]]

## Research gap
Prior transformer-based segmentation methods like SETR adopted ViT backbones that produce only single-scale, low-resolution features and rely on complex decoders. They also depend on positional encodings that degrade performance when test resolution differs from training. Existing methods largely focused on encoder design while neglecting the decoder's contribution to efficiency and accuracy.

## Contributions
- A novel hierarchical transformer encoder (Mix Transformer / MiT) that produces multi-scale features without positional encoding, enabling flexible test-time resolution.
- A lightweight All-MLP decoder that aggregates local and global attention from different encoder layers, replacing complex decoder modules.
- A family of models (SegFormer-B0 to B5) spanning real-time to high-performance, achieving state-of-the-art results on ADE20K (51.8% mIoU), Cityscapes (84.0% mIoU), and COCO-Stuff while being significantly smaller and faster than prior methods.
- Demonstration of strong zero-shot robustness to common corruptions on Cityscapes-C, outperforming DeepLabV3+ variants by large margins.

## Method
**Hierarchical Transformer Encoder (MiT):** Uses overlapping patch embeddings across four stages with progressively increasing channels and decreasing spatial resolution. Each stage contains transformer blocks with Efficient Self-Attention (reduction ratios decrease from 8 to 1 across stages) and Mix-FFN — a feed-forward network with 3×3 depthwise convolutions that implicitly encodes positional information, eliminating the need for explicit positional encodings.

**All-MLP Decoder:** Takes multi-scale features from all four encoder stages, unifies their channel dimensions via MLP layers, upsamples to a common resolution, concatenates them, and applies a final MLP to predict segmentation masks. The simplicity works because the transformer encoder's effective receptive field is much larger than CNNs, so a lightweight decoder suffices for global reasoning.

**Model scaling:** Six variants (B0–B5) are defined by varying channel dimensions, number of encoder layers per stage, attention heads, and expansion ratios, following ResNet-like design principles where Stage 3 receives the most computation.

## Datasets & evaluation
- **ADE20K:** SegFormer-B5 achieves 51.8% mIoU (SOTA), with B0 at 37.4% using only 3.8M params.
- **Cityscapes:** B5 reaches 84.0% mIoU on val, 83.1% on test with Mapillary pre-training. B0 achieves 76.2% at 15.2 FPS real-time.
- **COCO-Stuff:** B5 achieves 46.7% mIoU with 84.7M params (4× smaller than SETR).
- **Cityscapes-C (robustness):** Up to 588% relative improvement on Gaussian noise and 295% on snow corruption compared to DeepLabV3+ variants.

## Limitations
- Unclear whether the smallest model (3.7M params) can run on edge devices with ~100K memory budgets.
- Relies on ImageNet pre-training for the encoder.
- Evaluation focused on standard benchmarks; applicability to more diverse or specialized segmentation tasks not explored.

## Key takeaways
- Eliminating positional encodings in favor of Mix-FFN with depthwise convolutions yields both better accuracy and robustness to resolution changes — a finding with broad implications for vision transformer design.
- A simple MLP decoder is sufficient when paired with a transformer encoder that provides large effective receptive fields, challenging the assumption that complex decoders are necessary.
- The SegFormer architecture demonstrates that jointly redesigning encoder and decoder for transformers produces better efficiency-accuracy tradeoffs than adapting CNN decoder designs to transformer backbones.
