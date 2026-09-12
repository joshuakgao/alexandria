---
title: "Change3D: Revisiting Change Detection and Captioning from A Video Modeling Perspective"
authors: [Duowang Zhu, Xiaohu Huang, Haiyan Huang, Hao Zhou, Zhenfeng Shao]
year: 2025
venue: "CVPR 2025"
tags: [change-detection, remote-sensing]
url: "https://openaccess.thecvf.com/content/CVPR2025/papers/Zhu_Change3D_Revisiting_Change_Detection_and_Captioning_from_A_Video_Modeling_CVPR_2025_paper.pdf"
date_ingested: 2026-09-10
---

# Change3D: Revisiting Change Detection and Captioning from A Video Modeling Perspective

![[2025-change3d-thumbnail.png]]

## Research gap

Existing bi-temporal change detection and captioning methods follow a two-stage paradigm: a shared-weight image encoder extracts spatial features independently from each image, then a dedicated change extractor captures differences between them. This creates two problems. First, image encoding is task-agnostic — most parameters are spent on independent spatial feature extraction rather than on modeling changes, yielding an unreasonable parameter distribution. Second, different tasks (binary change detection, semantic change detection, building damage assessment, change captioning) each require separately designed change extractors, preventing a unified framework.

## Contributions

- Reconceptualizes bi-temporal change detection and captioning as a video modeling problem by treating image pairs as two-frame videos with learnable perception frames inserted between them.
- Eliminates the need for dedicated change extractors entirely — a single video encoder handles feature extraction and change modeling jointly.
- Provides a unified framework applicable to binary change detection, semantic change detection, building damage assessment, and change captioning without architectural redesign.
- Achieves state-of-the-art performance across eight benchmarks while using only ~6–13% of the parameters and ~8–34% of the FLOPs of existing methods.

## Method

Change3D inserts learnable **perception frames** (initialized randomly, same spatial dimensions as the input images) between the two bi-temporal images along the temporal dimension, forming a short pseudo-video. The number of perception frames depends on the task: 1 for binary change detection, 3 for semantic change detection (one per semantic map plus binary), 2 for building damage assessment (localization + classification), and 1 for change captioning.

A pre-trained video encoder (X3D-L by default, pre-trained on Kinetics-400) processes the concatenated volume. Through spatiotemporal attention, the perception frames interact directly with the bi-temporal images to discern differences. Multi-layer perception features are extracted for detection tasks; the deepest-layer perception feature is used for captioning.

A 1×1 convolution with ReLU integrates bi-temporal difference features into the perception features. For detection, a simple cascade decoder (transposed convolutions + skip connections) progressively upsamples perception features to full resolution. For captioning, a transformer decoder with masked self-attention and cross-attention generates change descriptions conditioned on the perception feature.

Joint loss functions are used: cross-entropy + dice loss for BCD and BDA; cross-entropy + dice + cosine similarity loss for SCD; cross-entropy loss for CC.

## Datasets & evaluation

Evaluated on **eight benchmarks** across four tasks:

- **Binary change detection**: LEVIR-CD (F1 91.82), WHU-CD (F1 94.56), CLCD (F1 78.03) — all SOTA.
- **Semantic change detection**: HRSCD (F1 73.29, SeK 26.85), SECOND (F1 62.83, SeK 22.98) — all SOTA.
- **Building damage assessment**: xBD (F_overall 79.42, F_cls 76.71) — SOTA on classification and overall scores.
- **Change captioning**: LEVIR-CC (CIDEr 138.29), DUBAI-CC (CIDEr 86.19) — SOTA on both.

Change3D with X3D-L uses only 1.54–5.05M parameters depending on task, with the fastest inference speed among all compared methods.

## Limitations

- Relies on pre-trained video encoders from action recognition (Kinetics-400); performance drops significantly without pre-training, and the domain gap between action recognition and remote sensing change detection is bridged empirically rather than theoretically justified.
- The perception frame concept is effective but somewhat opaque — the learned perception frames lack interpretability beyond attention visualizations.
- Evaluated exclusively on remote sensing datasets with aligned, registered bi-temporal image pairs; applicability to unaligned or multi-view settings (as in VSCD or SceneDiff) is not explored.
- The simple cascade decoder is intentionally minimal to highlight the video encoder's capability, but a stronger decoder might further improve results.

## Key takeaways

- Reframing bi-temporal change detection as video understanding is surprisingly effective — a lightweight video model (X3D-L, ~1.5M params) outperforms image-based models with 10–100x more parameters.
- Learnable perception frames act as task-specific queries that aggregate change information through spatiotemporal interaction, functioning similarly to CLS tokens in vision transformers but in the temporal domain.
- The sandwiched insertion position (I₁, perception frames, I₂) is critical — placing perception frames at the edges degrades performance because video encoding models inter-frame interaction most strongly between adjacent frames.
- Pre-trained video features transfer well from action recognition to remote sensing change detection despite the large domain gap, suggesting that temporal difference modeling is a transferable capability.
- The unified framework across four distinct tasks demonstrates that the core operation in change detection/captioning is temporal difference extraction, which video models naturally perform.
