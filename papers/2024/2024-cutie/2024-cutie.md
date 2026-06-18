---
title: "Putting the Object Back into Video Object Segmentation"
authors: [Ho Kei Cheng, Seoung Wug Oh, Brian Price, Joon-Young Lee, Alexander Schwing]
year: 2024
tags: [segmentation]
url: ""
pdf: "[[2024-cutie.pdf]]"
date_ingested: 2026-06-18
---

# Putting the Object Back into Video Object Segmentation

![[2024-cutie-thumbnail.png]]

## Research gap

Cutie introduces object-level memory reading to video object segmentation (VOS), addressing a fundamental weakness of prior pixel-level matching approaches like XMem. Where pixel-level methods independently match each query pixel to memory pixels via attention, producing noisy correspondences especially with distractors, Cutie uses a small set of learned object queries that iteratively probe and calibrate pixel features through an object transformer. This top-down/bottom-up bidirectional communication enriches pixel features with object-level semantics while retaining high-resolution spatial detail for accurate segmentation.

## Contributions

- Object-level memory reading via a query-based object transformer that bidirectionally communicates between learned object queries and high-resolution pixel features
- Foreground-background masked attention that cleanly separates target and distractor semantics by constraining which regions object queries can attend to
- Compact object memory that summarizes target features via mask pooling with foreground-background separation and positional embeddings
- Careful architectural choices that avoid any direct attention between high-resolution spatial features, keeping compute efficient (O(NHW) instead of O(H²W²))
- Strong results on challenging MOSE benchmark (+8.7 J&F over XMem) while being 3x faster than DeAOT

## Method

**Object Transformer**: Takes pixel readout R₀ (from XMem-style pixel memory), N=16 learned object queries X, and object memory S. L=3 transformer blocks perform bidirectional communication: object queries attend to pixel features (masked cross-attention), then self-attend for global reasoning, then inject semantics back into pixel features (reversed cross-attention). Pixel FFN uses convolutions instead of self-attention to avoid O(n⁴) cost.

**Foreground-Background Masked Attention**: An intermediate mask M_l is predicted from pixel features at each layer. The first N/2 object queries attend only to foreground (M_l ≥ 0.5), the rest only to background. This hard separation is bridged by subsequent self-attention, combining clean semantics with global interaction.

**Object Memory**: N vectors computed by mask pooling over encoded memory features with N different masks (foreground/background separated, position-aware). Updated via streaming average for constant time/memory. Provides target-specific positional embeddings to the object queries.

**Pixel Memory**: Derived from XMem — attentional component (keys/values) + recurrent component (hidden state). FIFO management with optional long-term memory for long videos.

## Datasets & evaluation

On MOSE validation: 68.3 J&F (Cutie-base, trained with MOSE) vs. 59.6 for XMem and 64.1 for DeAOT. On DAVIS-17 val: 88.8 J&F. On YouTubeVOS-2019 val: 86.1 G. On BURST test: 62.6 HOTA with only 2.36G memory vs. 57.9 for DeAOT at 34.9G. Runs at 45.5 FPS (small) / 36.4 FPS (base) — 3x faster than DeAOT, comparable to XMem. Ablations show L=3 transformer blocks and N=16 queries are optimal efficiency/accuracy trade-offs.

## Limitations

- Still uses ImageNet-pretrained ConvNet backbones (ResNet-18/50); vision transformer backbones could further improve but at efficiency cost
- The foreground-background split is binary (threshold 0.5) — soft or learned thresholds might handle ambiguous regions better
- Object queries are fixed at N=16; adaptive allocation based on object complexity is unexplored
- Performance gap between standard (DAVIS) and challenging (MOSE) benchmarks remains significant (~20 J&F), indicating room for improvement on hard cases
- Does not address the initial segmentation problem — requires first-frame annotation, unlike SAM-based approaches that can initialize from prompts

## Key takeaways

The object transformer consists of L blocks (default 3), each performing: (1) foreground-background masked cross-attention from object queries to pixel features, (2) self-attention among object queries, and (3) reversed cross-attention putting object semantics back into pixel features. A key design is the foreground-background masked attention that splits object queries into two groups — half attend only to foreground, half only to background — enabling clean semantic separation between target and distractors. A compact object memory stores mask-pooled summaries of target objects across frames, providing target-specific features to the object queries.

Cutie achieves large improvements on challenging benchmarks: +8.7 J&F over XMem on MOSE (heavy occlusions and crowded environments) while remaining competitive on standard DAVIS and YouTubeVOS benchmarks. It runs at 36–45 FPS, making it real-time capable.

Cutie is a direct successor to XMem (same authors), retaining XMem's pixel memory architecture but adding the object transformer on top. Where XMem improved long-term memory management, Cutie improves the quality of memory reading itself. The object transformer draws from DETR-style query-based detection (Mask2Former's masked attention) but adapts it for VOS with bidirectional pixel-query communication and foreground-background masking.

Compared to DEVA, which decouples image segmentation from temporal propagation, Cutie is a tighter integration — the object transformer operates within the propagation pipeline rather than as a separate module. SAM-Track wraps existing VOS methods; Cutie improves the VOS core itself.

Cutie serves as the VOS backbone in several downstream systems: DEVA uses XMem/Cutie for propagation, and Cutie's robustness to distractors makes it particularly valuable for open-world and large-vocabulary settings.
