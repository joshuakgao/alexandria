---
title: "Revisiting Feature Prediction for Learning Visual Representations from Video"
authors: [Adrien Bardes, Quentin Garrido, Jean Ponce, Xinlei Chen, Michael Rabbat, Yann LeCun, Mahmoud Assran, Nicolas Ballas]
year: 2024
venue: "ICLR 2025"
tags: [encoder, world-models, video-understanding]
url: "https://arxiv.org/abs/2404.08471"
date_ingested: 2026-07-04
---

# Revisiting Feature Prediction for Learning Visual Representations from Video

![[2024-v-jepa-thumbnail.png]]

## Research gap
Prior self-supervised video representation learning relied on pixel-level reconstruction (VideoMAE, OmniMAE, Hiera), which dedicates significant model capacity to low-level visual details and requires long training schedules. Feature prediction had shown promise in images (I-JEPA) and audio (data2vec), but its effectiveness as a stand-alone objective for video — without pretrained encoders, text supervision, negative examples, or reconstruction — remained unexplored with modern tools (ViTs, masked modeling, JEPA frameworks).

## Contributions
- V-JEPA: a family of vision models (ViT-L/16, ViT-H/16, ViT-H/16₃₈₄) trained purely via self-supervised feature prediction on 2M videos, with no pixel reconstruction, text, or pretrained encoders.
- Demonstrates that feature prediction produces versatile frozen representations excelling on both motion-based (SSv2) and appearance-based (K400) tasks simultaneously — outperforming all prior video methods in frozen evaluation.
- Shows feature prediction is significantly more label-efficient than pixel reconstruction: the performance gap widens as labeled data decreases.
- Feature prediction trains ~2× faster than comparable pixel prediction methods.
- Qualitative analysis via a learned diffusion decoder shows V-JEPA predictions are spatially and temporally grounded, capturing object permanence and consistent motion.

## Method
- **Architecture**: Joint-Embedding Predictive Architecture (JEPA) with a ViT encoder and a narrow Transformer predictor (12 blocks, dim 384). The encoder processes visible video tokens; the predictor maps encoder outputs + learnable mask tokens to representations of masked regions.
- **Training objective**: L1 regression between predictor outputs and stop-gradient EMA encoder targets. The EMA + stop-gradient + predictor prevents representation collapse; theoretically, the optimal predictor computes the conditional median, forcing the encoder to maximize information capture.
- **Masking strategy**: Multi-block masking — union of spatially continuous blocks repeated across the full temporal dimension, achieving ~90% masking ratio. Both short-range (8 blocks × 15%) and long-range (2 blocks × 70%) masks are used. This outperforms random-tube and causal masking strategies.
- **Data**: VideoMix2M — ~2M videos from HowTo100M, Kinetics-400/600/700, and Something-Something-v2 (validation sets removed).
- **Evaluation**: Attentive probing (learned cross-attention pooling) on frozen backbones, which provides +17pp over average pooling. Also evaluated with end-to-end fine-tuning.

## Datasets & evaluation
- **Video tasks**: Kinetics-400 (appearance-based action recognition), Something-Something-v2 (motion classification), AVA (action localization).
- **Image tasks**: ImageNet-1K, Places205, iNaturalist 2021.
- **Key results (frozen evaluation, ViT-H/16)**: K400 82.0%, SSv2 71.4%, AVA 25.8%, IN1K 75.9%. Outperforms all prior video models (VideoMAE, VideoMAEv2, OmniMAE, Hiera, MVD) on every video and image task. Narrows the gap with large image models (DINOv2, OpenCLIP) on appearance tasks while gaining +21pp on motion tasks (SSv2).
- **Label efficiency**: With 5% labels, V-JEPA drops only 12% on K400 vs. 30% for VideoMAEv2 and 15.9% for VideoMAE.
- **Fine-tuning**: Best ViT-L/16 performance; matches Hiera-L on SSv2 with significantly fewer samples seen.

## Limitations
- Video pretraining datasets (VideoMix2M) lack the visual diversity of internet-scale image datasets (LVD-142M, LAION), likely limiting image task performance relative to DINOv2/OpenCLIP.
- Evaluation is limited to classification and localization; generative capabilities, dense prediction, and robotics applications are not explored.
- The attentive probing protocol, while more informative than linear probing, introduces a learned component that complicates interpretation of "frozen" representation quality.
- Multi-block masking with full temporal extension may not be optimal for all video domains (e.g., very long videos or videos with rapid scene changes).

## Key takeaways
- Feature prediction in representation space is a more effective and efficient self-supervised objective for video than pixel reconstruction — it avoids wasting capacity on low-level details while capturing both appearance and motion.
- The theoretical connection to minimizing conditional median absolute deviation provides principled understanding of why the EMA + predictor architecture prevents collapse.
- V-JEPA's frozen representations are remarkably versatile: a single backbone, without any parameter adaptation, handles motion-heavy, appearance-heavy, and image tasks competitively. This makes V-JEPA an attractive general-purpose video encoder.
- The label efficiency advantage suggests V-JEPA representations are more semantically structured than pixel-reconstructive alternatives, requiring less supervision to extract task-relevant information.
