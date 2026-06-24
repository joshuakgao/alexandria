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

*Last updated: 2026-06-18 | Sources: 6 papers*

## Current thesis

Video segmentation is evolving along two complementary axes: **modular architectures** that decouple image segmentation from temporal propagation (DEVA), and **improved memory reading** that addresses the quality of pixel-level matching itself (Cutie). The field shows a clear trajectory: XMem (2022) solved long-term memory management, Cutie (2024) solved memory reading quality via object-level queries, SAM-Track (2023) added interactive control, DEVA (2023) introduced principled fusion of per-frame and temporal signals, and SAMURAI (2024) incorporated motion awareness. Cutie's +8.7 J&F improvement over XMem on MOSE demonstrates that object-level reasoning is critical for challenging scenarios with distractors and occlusions — the remaining frontier for VOS. The emerging consensus is that robust VOS requires both good memory management (what to store) and good memory reading (how to use it), combined with foundation model integration for open-world applicability.

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

## Open problems

- How to effectively leverage motion without over-fitting to specific motion patterns
- Balancing zero-shot generalization with task-specific performance
- Scaling to longer video sequences without exponential memory overhead
- Handling extreme occlusions and appearance changes beyond current capabilities
- Optimal fusion strategies: DEVA's IoU-based merging is effective but may struggle with heavy occlusions; learned merging could improve robustness
- Whether lightweight transformer segmentation models (e.g. SegFormer-B0 at 3.7M params) can run on edge devices with ~100K memory constraints

## Contradictions and debates

- **End-to-end vs. decoupled**: End-to-end methods (MinVIS, Video-K-Net) outperform on small-vocabulary benchmarks with abundant data; DEVA shows decoupled approaches dominate in data-scarce and large-vocabulary settings. The crossover point is unclear.
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
