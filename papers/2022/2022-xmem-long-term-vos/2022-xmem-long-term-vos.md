---
title: "XMem: Long-Term Video Object Segmentation with an Atkinson-Shiffrin Memory Model"
authors: [Ho Kei Cheng, Alexander Schwing]
year: 2022
tags: [segmentation, video-understanding, cognitive-ai]
url: ""
pdf: "[[2022-xmem-long-term-vos.pdf]]"
date_ingested: 2026-06-18
---

# XMem: Long-Term Video Object Segmentation with an Atkinson-Shiffrin Memory Model

![[2022-xmem-long-term-vos-thumbnail.png]]

## Research gap

XMem addresses the memory consumption vs. accuracy trade-off in video object segmentation by introducing a hierarchical memory architecture inspired by cognitive psychology (Atkinson-Shiffrin model). The system maintains three independent yet interconnected memory stores: sensory memory (rapidly updated), working memory (high-resolution), and long-term memory (compact and persistent). A novel memory potentiation algorithm consolidates relevant working memory features into long-term storage, preventing memory explosion while maintaining performance over extended videos. XMem achieves state-of-the-art on long-video benchmarks while remaining competitive on short-video datasets.

## Contributions

- **Hierarchical memory architecture**: Adapts Atkinson-Shiffrin cognitive model to segmentation with three memory types (sensory, working, long-term)
- **Memory potentiation algorithm**: Consolidates actively used working memory elements into long-term storage, avoiding memory explosion
- **New memory reading mechanism**: Efficiently queries multiple memory stores during inference
- **Long-video capability**: First method to effectively handle very long videos (2000+ frames) without quality degradation
- **Unified performance**: Matches or exceeds state-of-the-art on both long and short video benchmarks

## Method

**Architecture**: Encoder-decoder framework with three memory modules:
- **Sensory memory**: Stores current frame features, rapidly updated (single frame)
- **Working memory**: High-resolution feature maps with limited capacity (N recent frames)
- **Long-term memory**: Compact consolidated representation with larger capacity

**Memory Potentiation**: At each timestep, compute importance scores for working memory elements. High-importance features are consolidated into long-term memory via learned transformations, while low-importance features are discarded. This prevents quadratic memory growth while maintaining predictive accuracy.

**Memory Reading**: Attention-based mechanism that reads from all three memory stores simultaneously, allowing temporal information to flow across long time gaps.

## Datasets & evaluation

- **DAVIS 2017**: Achieves 87.9% J&F on validation set (state-of-the-art at publication)
- **Long-video datasets**: Significant improvements on videos >1000 frames
- **Computational efficiency**: Real-time or near-real-time inference despite long-video capability
- **Robustness**: Maintains stable performance across entire video length without drift

## Limitations

- Memory potentiation hyperparameters may require tuning for different video domains
- Semantic understanding of "importance" for memory consolidation remains hand-crafted rather than fully learned
- Performance on extreme scenarios (very dense objects, rapid appearance changes) not thoroughly explored
- Generalization to novel object categories at test time not explicitly addressed

## Key takeaways

Extends [[2023-tracking-anything|TrackAnything]] and other VOS methods by solving the memory bottleneck. Contrasts with online learning methods (require test-time training) and recurrent methods (prone to drift). Inspired by cognitive science: memory consolidation is fundamental to human long-term learning. Precedes and provides foundation for later work like SAM 2 and SAMURAI.
