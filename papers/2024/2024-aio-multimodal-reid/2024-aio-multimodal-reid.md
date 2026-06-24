---
title: "All in One Framework for Multimodal Re-identification in the Wild"
authors: [He Li, Mang Ye, Ming Lin, Bo Du]
year: 2024
venue: "CVPR 2024"
tags: [re-id, vision-language-models]
url: ""
pdf: "[[2024-aio-multimodal-reid.pdf]]"
date_ingested: 2026-06-18
---

# All in One Framework for Multimodal Re-identification in the Wild

![[2024-aio-multimodal-reid-thumbnail.png]]

## Research gap

AIO (All-in-One) tackles a fundamental practical limitation of existing ReID methods: real-world scenarios present uncertain query modalities (RGB, infrared, sketch, text, or combinations thereof), but current approaches train separate models for each modality pair and cannot generalize across them. AIO is the first framework to simultaneously handle all four commonly used ReID modalities in a single unified model.

## Contributions

- First framework to handle all four common ReID modalities (RGB, IR, sketch, text) and arbitrary combinations in a single unified model
- Frozen foundation model strategy that preserves zero-shot capabilities while learning modality-specific tokenizers
- Three complementary cross-modal heads: CE for identity, VA for fine-grained attribute modeling, FB for multimodal alignment
- Missing modality synthesis via Channel Augmentation (IR) and Lineart (sketch) with progressive learning
- Strong zero-shot performance across cross-modal and multimodal ReID benchmarks without task-specific fine-tuning

## Method

**Architecture:**
1. **Multimodal Tokenizer**: Modality-specific projectors — IBN-style conv tokenizers for RGB/IR/sketch (with channel replication for single-channel inputs), CLIP tokenizer for text. A learnable multimodal [CLS] token is appended.
2. **Frozen Multimodal Encoder**: ViT pre-trained on LAION-2B via contrastive learning. All parameters frozen. Processes concatenated multimodal embeddings with position encodings.
3. **Cross-modal Heads**:
   - *CE*: Bottleneck + classifier with cross-entropy loss for identity classification
   - *VA*: Masks attribute keywords in text, concatenates masked text features with RGB features, and predicts masked words — learns fine-grained vision-text alignment
   - *FB*: Supervised feature binding loss that attracts all modality features toward RGB (the most common modality), while pushing apart features of different identities

**Missing Modality Synthesis**: Channel Augmentation generates synthetic IR from RGB; Lineart generates synthetic sketches. Feature distributions of synthetic images bridge the gap between RGB and real IR/sketch.

**Progressive Learning**: Stage 1 (40 epochs) trains on synthetic multimodal data; Stage 2 (80 epochs) fine-tunes on real paired data from LLCM (IR) and MaSk1K (sketch).

## Datasets & evaluation

- **Zero-shot cross-modal**: R→R 79.6 Rank-1 (Market1501), I→R 57.6 (SYSU-MM01), S→R 70.2 (PKU-Sketch), T→R 53.4 (CUHK-PEDES)
- **Multimodal**: S+T→R 92.1 Rank-1 on Tri-CUHK-PEDES; R+I+S+T achieves 58.6 Rank-1
- Adding more modalities consistently improves performance (R+T: 56.5 → R+I+T: 57.8 → R+I+S+T: 58.6)
- LAION-2B pre-trained ViT outperforms ViT-ICS, UniCL, and CLIP as backbone choices
- Ablation shows each head is critical: FB provides the largest boost for cross-modal tasks (I→R: +13.9, S→R: +23.4)

## Limitations

- Person-specific: AIO is designed and evaluated only for person ReID, not generalizable objects
- Synthetic IR/sketch may not fully capture real-world domain shifts (e.g., thermal noise patterns, artistic sketch styles)
- The progressive learning strategy introduces training complexity and sensitivity to epoch scheduling
- Frozen encoder means the model cannot adapt its representations — performance ceiling may be lower than fine-tuned approaches when sufficient data exists
- Evaluation of multimodal combinations with IR relies on synthetic (CA-generated) IR rather than real IR images

## Key takeaways

The key design choice is using a frozen pre-trained foundation model (ViT pre-trained on LAION-2B) as a shared multimodal encoder. Diverse modalities are projected into a unified token space via lightweight modality-specific tokenizers, then concatenated and fed into the frozen encoder. Since the encoder is frozen, AIO only trains the tokenizers and cross-modal heads, preserving the foundation model's zero-shot capabilities. Three cross-modal heads guide learning: a Classification head (CE) for identity-invariant features, a Vision Guided Masked Attribute Modeling head (VA) that learns fine-grained RGB-text relationships by predicting masked attribute words from paired images, and a Multimodal Feature Binding head (FB) that aligns all modalities toward RGB features using a supervised contrastive loss.

To handle the scarcity of paired multimodal data (especially IR and sketch), AIO uses Channel Augmentation and Lineart to synthesize missing modalities from RGB, with a progressive learning strategy that trains on synthetic data first before fine-tuning on real paired data.

AIO addresses a gap that **VICP** (2025) tackles from a different angle. While VICP focuses on generalizing ReID to unseen *object categories* via LLM-guided prompting, AIO focuses on generalizing across *input modalities* — handling uncertain combinations of RGB, IR, sketch, and text for person ReID. Both share the philosophy of using frozen foundation models to preserve generalization.

**UNIReID** and **TriReID** handle up to 3 modalities but require complex modality-specific architectures. AIO's tokenizer + frozen encoder design is simpler and scales to 4 modalities with arbitrary combinations.

The frozen encoder strategy relates to **ImageBind**'s approach of aligning all modalities to a shared space, but AIO introduces ReID-specific heads (VA, FB) that are critical for identity-discriminative learning.
