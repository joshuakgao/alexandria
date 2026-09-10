---
topic: Segmentation
slug: segmentation
---

# Segmentation

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "segmentation")
SORT year DESC
```

## Overview

*Last updated: 2026-09-03 | Sources: 9 papers*

## Current thesis

Video segmentation is evolving along two complementary axes: **modular architectures** that decouple image segmentation from temporal propagation (DEVA), and **improved memory reading** that addresses the quality of pixel-level matching itself (Cutie). The field shows a clear trajectory: XMem (2022) solved long-term memory management, Cutie (2024) solved memory reading quality via object-level queries, SAM-Track (2023) added interactive control, DEVA (2023) introduced principled fusion of per-frame and temporal signals, SAMURAI (2024) incorporated motion awareness, EfficientTAM (2024) demonstrated that near-SAM 2 quality is achievable with plain ViTs at mobile-friendly latencies, and EdgeTAM (2025) pushed on-device VOS to 16 FPS by identifying memory attention — not just the encoder — as the true bottleneck and compressing it with a learned 2D Spatial Perceiver. The emerging consensus is that robust VOS requires both good memory management (what to store) and good memory reading (how to use it), combined with foundation model integration for open-world applicability.

On the image segmentation side, SegFormer (2021) demonstrated that jointly redesigning encoder and decoder for transformers — using hierarchical multi-scale features and a lightweight All-MLP decoder — can dramatically outperform both CNN-based methods and earlier ViT adaptations (SETR) in efficiency, accuracy, and robustness.

## Key trends

- **Transformers replacing CNNs for segmentation backbones**: SegFormer (2021) showed that hierarchical vision transformers with multi-scale features eliminate the need for complex decoders. A simple MLP decoder suffices when the encoder provides large effective receptive fields, challenging the assumption that segmentation requires elaborate decoder modules like ASPP or PPM.
- **Positional-encoding-free transformers**: SegFormer's Mix-FFN with depthwise convolutions implicitly encodes positional information, avoiding resolution-dependent positional encodings and improving robustness to test-time resolution changes.
- **Decoupled over end-to-end**: DEVA (2023) shows that separating image segmentation from temporal propagation generalizes better than end-to-end training, especially for large-vocabulary and open-world settings. This challenges the default assumption that joint training is always better.
- **Foundation model integration**: The decoupled paradigm enables plug-and-play use of SAM, Grounding-DINO, and other foundation models — no video-level retraining needed. SAM-Track and DEVA both exploit this modularity.
- **Temporal denoising**: In-clip consensus (DEVA) demonstrates that per-frame segmentation quality can be improved through temporal agreement, a principle that goes beyond simple tracking.
- **Hierarchical memory systems**: XMem (2022) established that cognitive-inspired multi-level memory solves the long-video problem; SAMURAI (2024) applies similar principles to tracking; DEVA uses XMem as its propagation backbone.
- **Object-level memory reading**: Cutie (2024) shows that replacing pixel-level matching with object queries that bidirectionally communicate with pixel features dramatically improves robustness to distractors. The object transformer adds top-down reasoning without sacrificing high-resolution features, bridging the gap between query-based detection (Mask2Former) and memory-based VOS.
- **Multimodal user control**: SAM-Track (2023) shows that interactive prompting (clicks, boxes, text) increases applicability to real-world workflows
- **Motion-aware tracking**: SAMURAI (2024) adds motion modeling on top of SAM 2, complementing the appearance-based approaches
- **NAS-optimized instance segmentation**: RF-DETR-Seg (2026) shows that lightweight segmentation heads on detection transformers, combined with weight-sharing NAS, can discover Pareto-optimal accuracy-latency tradeoffs for real-time instance segmentation from a single training run.
- **Plain ViTs for efficient VOS**: EfficientTAM (2024) demonstrates that SAM 2's hierarchical Hiera encoder can be replaced with vanilla ViT-Tiny/Small at ~2x speedup and ~2.4x parameter reduction with minimal quality loss. Memory spatial token redundancy enables simple 2×2 average pooling to approximate full cross-attention, unlocking ~10 FPS on-device video segmentation.
- **Memory attention as the true on-device bottleneck**: EdgeTAM (2025) shows that even after encoder compression, memory cross-attention over dense frame-level features dominates on-device latency. Its 2D Spatial Perceiver — splitting learned queries into global and patch-level groups — compresses memories while preserving spatial structure, achieving 16 FPS on iPhone with 8x memory attention speedup. Two-stage distillation (image + memory features) from SAM 2 recovers accuracy without inference cost.

## Open problems

- How to effectively leverage motion without over-fitting to specific motion patterns
- Balancing zero-shot generalization with task-specific performance
- Scaling to longer video sequences without exponential memory overhead
- Handling extreme occlusions and appearance changes beyond current capabilities
- Optimal fusion strategies: DEVA's IoU-based merging is effective but may struggle with heavy occlusions; learned merging could improve robustness
- Whether lightweight transformer segmentation models (e.g. SegFormer-B0 at 3.7M params) can run on edge devices with ~100K memory constraints
- Whether architecture-level augmentation (randomly sampling sub-net configurations during training) can serve as a general regularizer for segmentation models beyond detection
- Whether adaptive (scene-dependent) memory compression can improve on EdgeTAM's fixed-query 2D Spatial Perceiver for complex multi-object scenarios

## Contradictions and debates

- **End-to-end vs. decoupled**: End-to-end methods (MinVIS, Video-K-Net) outperform on small-vocabulary benchmarks with abundant data; DEVA shows decoupled approaches dominate in data-scarce and large-vocabulary settings. The crossover point is unclear.
- **Hierarchical vs. plain encoders for VOS**: SAM 2's Hiera provides multi-scale features but EfficientTAM shows a single-scale plain ViT is nearly as effective (74.5 vs 74.7 J&F on SA-V) at far lower cost. The marginal benefit of hierarchical features for temporal propagation may not justify the efficiency penalty.
- **Pixel-level vs. object-level memory reading**: XMem uses pure pixel-level matching; Cutie adds object-level queries on top. Cutie shows massive gains on challenging data (MOSE) while being comparable on standard benchmarks, suggesting pixel-level matching is sufficient for easy cases but breaks down with distractors.
- **Zero-shot vs. supervised**: SAMURAI achieves competitive zero-shot performance, but fully supervised trackers still lead on some benchmarks
- **Motion vs. appearance**: Different domains prioritize these cues differently (tracking requires motion, fine-grained segmentation may rely more on appearance)
- **Complex vs. simple decoders**: SegFormer shows MLP decoders beat complex CNN decoders when paired with transformer encoders, but this may not hold for CNN backbones with smaller receptive fields.

## Recommended reading order

For someone new to this topic:
1. **SegFormer** — Understand how transformers changed image segmentation with hierarchical multi-scale features and simple MLP decoders
2. **XMem** — Understand long-video memory challenges and hierarchical memory architectures (foundational temporal propagation)
3. **Cutie** — See how object-level memory reading improves on XMem's pixel matching; same authors, direct successor
4. **DEVA** — See how decoupling image segmentation from temporal propagation enables open-world video segmentation; uses XMem/Cutie as backbone
5. **SAM-Track** — Compare DEVA's automatic approach with interactive user-in-the-loop segmentation and tracking
6. **SAMURAI** — See how motion modeling and selective memory retention improve on SAM 2 for visual object tracking
7. **EfficientTAM** — See how plain ViTs and efficient memory cross-attention make SAM 2-level VOS practical on mobile devices
8. **EdgeTAM** — See how learned memory compression (2D Spatial Perceiver) and distillation push on-device VOS to 16 FPS
