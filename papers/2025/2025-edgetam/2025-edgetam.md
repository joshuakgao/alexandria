---
title: "EdgeTAM: On-Device Track Anything Model"
authors: [Chong Zhou, Chenchen Zhu, Yunyang Xiong, Saksham Suri, Fanyi Xiao, Lemeng Wu, Raghuraman Krishnamoorthi, Bo Dai, Chen Change Loy, Vikas Chandra, Bilge Soran]
year: 2025
venue: "CVPR 2025"
tags: [segmentation]
url: "https://arxiv.org/abs/2501.07256"
date_ingested: 2026-09-03
---

# EdgeTAM: On-Device Track Anything Model

![[2025-edgetam-thumbnail.png]]

## Research gap

Prior work on making SAM efficient (MobileSAM, FastSAM, EfficientSAM, EfficientTAM) focuses on compressing the image encoder, but benchmarking reveals that SAM 2's memory attention blocks are an equally critical latency bottleneck. Even replacing the image encoder with compact backbones like ViT-Tiny or RepViT yields only marginal speedup because the cross-attention over dense, long memory token sequences (~30K tokens) dominates on-device inference time. SAM 2's smallest variant runs at only ~1 FPS on iPhone 15 Pro Max.

## Contributions

- Identifies through systematic benchmarking that memory attention (specifically cross-attention over dense frame-level memories) is the primary latency bottleneck for on-device SAM 2 deployment, not just the image encoder.
- Proposes a **2D Spatial Perceiver** — a plug-in module that compresses frame-level memory features using learned queries split into global-level (attending to the full feature map) and patch-level (attending to local non-overlapping windows) groups, preserving spatial structure essential for dense prediction.
- Introduces a two-stage **distillation pipeline** that aligns both image encoder features and memory attention outputs between a SAM 2 teacher and the EdgeTAM student, improving accuracy without inference overhead.
- Achieves **16 FPS on iPhone 15 Pro Max** (6.4x faster than baseline) with 87.7, 70.0, 72.3, and 71.7 J&F on DAVIS 2017, MOSE, SA-V val, and SA-V test respectively — the first model to run unified segmentation and tracking on-device.

## Method

**Architecture**: Follows SAM 2's meta-architecture (image encoder, memory encoder, memory bank, memory attention, mask decoder) but replaces the image encoder with RepViT-M1 and reduces memory attention from 4 to 2 transformer blocks.

**2D Spatial Perceiver**: A lightweight plug-in module inserted before memory attention to compress dense frame-level memory features. Contains two types of learned queries:
- *Global queries* (Ng): each attends to the entire memory feature map via cross-attention followed by self-attention, producing frame-level summary vectors.
- *Patch-level queries* (Nl): each assigned to a non-overlapping local window of the memory feature map, preserving 2D spatial structure in the compressed output. Positional embeddings are added to the output (not input) to maintain explicit spatial information.

The module reduces memory attention complexity from O(T·C·H²·W²) to O(T·C·H·W·(Ng+Nl)), achieving ~8x speedup in memory attention with comparable performance. The speed-up ratio is controlled so (H·W)/(Ng+Nl) ≈ T.

**Distillation pipeline**: Two-stage knowledge transfer from SAM 2:
1. *Image pre-training stage*: MSE loss aligns student and teacher F16 image encoder features, alongside standard task losses.
2. *Video training stage*: adds a second MSE loss aligning memory attention output features FM between teacher and student, so memory-related modules also receive teacher supervision.

**Progressive fine-tuning**: After initial training on 8-frame sequences, the model is fine-tuned on 16-frame and then 32-frame sequences with the image encoder frozen, exploiting EdgeTAM's lower VRAM requirements.

## Datasets & evaluation

**Training**: SA-1B for image pre-training; SA-V + 10% SA-1B + DAVIS + MOSE + YTVOS for video training (130K iterations, batch size 256).

**Semi-supervised VOS**: EdgeTAM achieves 87.7 J&F on DAVIS 2017 (vs SAM 2's 90.9), 70.0 on MOSE (vs 75.8), 72.3 on SA-V val (vs 73.6), 71.7 on SA-V test (vs 76.8). Outperforms Cutie-base, XMem, and DEVA across benchmarks.

**Promptable VOS**: Across 9 datasets with 3-click prompts, EdgeTAM performs comparably to SAM 2 in both offline and online evaluation settings.

**Image segmentation**: On SA-23, EdgeTAM achieves competitive 1-click and 5-click mIoU with SAM and SAM 2.

**On-device performance**: 16 FPS on iPhone 15 Pro Max — 6.4x faster than the RepViT baseline without the 2D Spatial Perceiver (2.5 FPS) and dramatically faster than SAM 2 (~1 FPS).

## Limitations

- Performance gap to SAM 2 is more pronounced on challenging benchmarks (MOSE: 70.0 vs 75.8; SA-V test: 71.7 vs 76.8), suggesting the compression-accuracy tradeoff is steeper for complex multi-object and occlusion-heavy scenarios.
- The 2D Spatial Perceiver's fixed query count means the compression ratio is static regardless of scene complexity — adaptive compression could improve quality on hard frames.
- Distillation requires access to the full SAM 2 teacher during training, adding significant training cost.
- Progressive fine-tuning with longer sequences (16→32 frames) adds training stages but the marginal accuracy gain is not quantified per stage.

## Key takeaways

- For on-device VOS, the memory attention module — not just the image encoder — is the dominant bottleneck. This insight redirects efficient VOS research from encoder-only compression to holistic architecture optimization.
- Learned compression (Perceiver) outperforms naive spatial pooling for memory features, but only when spatial structure is preserved — the 2D Spatial Perceiver's patch-level queries are essential for dense prediction tasks.
- Two-stage distillation (image features + memory attention features) is more effective than image-only distillation, demonstrating that temporal reasoning modules benefit from dedicated knowledge transfer.
- On-device real-time VOS (16 FPS) is now achievable with reasonable quality, enabling applications like mobile AR, on-device video editing, and accessibility tools without server dependency.
