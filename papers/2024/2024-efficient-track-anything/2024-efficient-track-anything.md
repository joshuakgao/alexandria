---
title: "Efficient Track Anything"
authors: [Yunyang Xiong, Chong Zhou, Xiaoyu Xiang, Lemeng Wu, Chenchen Zhu, Zechun Liu, Saksham Suri, Balakrishnan Varadarajan, Ramya Akula, Forrest Iandola, Raghuraman Krishnamoorthi, Bilge Soran, Vikas Chandra]
year: 2024
venue: "ICCV 2025"
tags: [segmentation]
url: "https://arxiv.org/abs/2411.18933"
date_ingested: 2026-09-03
---

# Efficient Track Anything

![[2024-efficient-track-anything-thumbnail.png]]

## Research gap

SAM 2 achieves strong video object segmentation but its hierarchical image encoder (HieraB+, ~80M parameters) and long memory token sequences (~30K tokens) make it impractical for mobile and resource-constrained deployment. Even SAM 2's tiny variant achieves only comparable FPS to the base model due to the hierarchical design, leaving a gap for truly efficient track-anything models.

## Contributions

- Revisits plain, non-hierarchical ViT (ViT-Tiny/Small) as an image encoder for video object segmentation, showing it achieves competitive performance to SAM 2's hierarchical encoder with significantly fewer parameters.
- Proposes an efficient memory cross-attention mechanism that exploits the strong locality (spatial smoothness) of memory spatial tokens, using average pooling to create coarser key/value representations that closely approximate the original cross-attention.
- Delivers EfficientTAM models with state-of-the-art quality-efficiency tradeoffs: ~2x speedup on A100, ~2.4x parameter reduction vs SAM 2, and ~10 FPS on iPhone 15 Pro Max.

## Method

**Efficient image encoder**: Replaces SAM 2's hierarchical Hiera encoder with a plain vanilla ViT-Tiny or ViT-Small (16×16 patch size), initialized from SAMI-pretrained weights. Uses 14×14 non-overlapping windowed attention with 4 equally-spaced global attention blocks. Outputs a single-scale feature map at 16x reduced resolution — no multi-scale features fed to the decoder.

**Efficient memory cross-attention**: Observes that spatial memory tokens exhibit strong locality — consecutive tokens are similar. Applies 2×2 average pooling to memory spatial keys and values, reducing the token count by 4x. A correction constant `ln(lw × lh)` is added to the coarse spatial key attention scores to compensate for the pooling and balance attention between spatial and object pointer tokens. The resulting efficient cross-attention `softmax(A)Ṽ` closely approximates the original (relative Frobenius norm error ~0.03).

**Architecture**: Follows SAM 2's overall design (image encoder → memory encoder → memory bank → memory attention → mask decoder) but with the plain ViT encoder and efficient memory module. Pretrained on SA-1B for 90K steps, then fully trained on SA-V + 10% SA-1B for 300K steps on 256 A100 GPUs.

## Datasets & evaluation

**Training**: SA-1B (11M images, 1.1B masks) for pretraining; SA-V (51K videos, 600K masks) + 10% SA-1B for full training. Notably does not use additional open-source or internal datasets.

**Semi-supervised VOS**: On SA-V test, EfficientTAM-S achieves 74.5 J&F vs SAM 2's 74.7 with ~2x speedup and ~2.4x fewer parameters (34M vs 81M). Outperforms Cutie-base (61.6), XMem (60.1), and DEVA (53.8) by large margins. Competitive on MOSE (71.4 vs 72.8), DAVIS 2017 (89.2 vs 88.9), LVOS (73.4 vs 76.2), and YTVOS 2019 (87.2 vs 87.9).

**Promptable VOS**: With 8 annotated frames (3 clicks each), EfficientTAM-S achieves ~82 J&F offline and ~81 online, outperforming SAM+XMem++ and SAM+Cutie by >3 J&F.

**Image segmentation**: On SA-23, EfficientTAM achieves 60.7% mIoU vs SAM's 59.1% and SAM 2's 61.9%, with ~20x speedup and ~20x parameter reduction over SAM.

**Mobile deployment**: EfficientTAM-Ti/2 runs at 261ms/frame on iPhone 15 Pro Max; EfficientTAM-S/2 at 450ms. The efficient memory module provides >2x latency reduction on mobile.

## Limitations

- Performance gap to SAM 2 on challenging benchmarks like LVOS (73.4 vs 76.2) suggests the plain ViT encoder may lose some capacity for long-term temporal reasoning.
- The efficient memory cross-attention introduces a small approximation error that accumulates across transformer blocks; the ~0.03 relative error per block may compound in longer videos.
- Does not explore hybrid CNN-ViT architectures (MobileViT, EfficientViT) that could further improve mobile efficiency.
- Training still requires 256 A100 GPUs, limiting reproducibility.

## Key takeaways

- Plain ViTs are underrated for video segmentation — the hierarchical design of Hiera in SAM 2 adds complexity but not proportional quality gains, especially when efficiency matters.
- Memory spatial tokens in VOS models are highly redundant due to spatial smoothness, making simple average pooling an effective approximation strategy for cross-attention — more sophisticated efficient attention methods (Performer, Linformer) actually underperformed in preliminary experiments.
- The quality-efficiency Pareto frontier for VOS has shifted significantly: near-SAM 2 quality is achievable at mobile-friendly latencies (~10 FPS on iPhone), opening up on-device video segmentation applications.
