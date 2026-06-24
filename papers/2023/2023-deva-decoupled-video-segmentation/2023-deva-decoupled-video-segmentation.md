---
title: "Tracking Anything with Decoupled Video Segmentation"
authors: [Ho Kei Cheng, Seoung Wug Oh, Brian Price, Alexander Schwing, Joon-Young Lee]
year: 2023
venue: "ICCV 2023"
tags: [segmentation, video-understanding]
url: ""
pdf: "[[2023-deva-decoupled-video-segmentation.pdf]]"
date_ingested: 2026-06-18
---

# Tracking Anything with Decoupled Video Segmentation

![[2023-deva-decoupled-video-segmentation-thumbnail.png]]

## Research gap

DEVA (Decoupled Video Segmentation Approach) addresses the fundamental challenge that video segmentation training data is expensive to annotate, making end-to-end approaches difficult to scale to large-vocabulary or open-world settings. The key insight is to decouple the problem into two independent modules: a task-specific image-level segmentation model and a class/task-agnostic bi-directional temporal propagation model. This means only an image-level model needs to be trained for each new task (cheaper), while a universal temporal propagation model (based on XMem) is trained once and reused across all tasks.

## Contributions

- Decoupled formulation separating task-specific image segmentation from task-agnostic temporal propagation, enabling generalization to new tasks without video-level training
- Bi-directional propagation with in-clip consensus that denoises image-level segmentation errors and merges temporal propagation with new image segmentations
- State-of-the-art results on large-vocabulary video panoptic segmentation (VIPSeg) and open-world video segmentation (BURST)
- Demonstration that the approach seamlessly integrates with foundation models (SAM, Grounding-DINO) for open-world and text-prompted video segmentation

## Method

DEVA operates in a semi-online setting with two modules:

1. **Image segmentation model** (`Seg`): Any task-specific image segmenter (Mask2Former, EntitySeg, SAM, etc.) that produces per-frame segmentation hypotheses.

2. **Temporal propagation model** (`Prop`): A modified XMem that propagates segmentations forward through time using a memory bank of previously segmented frames. Class-agnostic, trained once on VOS datasets.

The pipeline works as follows:
- Initialize from the first frame using the image model
- Apply **in-clip consensus**: look at a small window of nearby frames, run the image model on each, and take the consensus segmentation (filtering out noisy/inconsistent detections)
- Propagate the consensus forward with the temporal model
- Periodically re-run the image model to detect new objects
- **Merge** propagated results with new in-clip consensus: match segments by IoU, keep propagated segments for existing objects, add unmatched new segments

The merging uses an IoU threshold to decide whether a new segment corresponds to an already-tracked object or is genuinely new. Segments below a confidence threshold are discarded as noise.

## Datasets & evaluation

- **VIPSeg** (large-vocabulary video panoptic segmentation): 45.8 VPQ, outperforming Video-K-Net (37.9) and MinVIS (43.4) with the same backbone
- **BURST** (open-world video segmentation): 50.5 OWTA with EntitySeg, significantly outperforming baselines (~25 OWTA). Especially strong on uncommon classes (53.3 vs. 23.9)
- **Ref-DAVIS/Ref-YouTubeVOS** (referring video segmentation): Competitive with end-to-end ReferFormer without any video-level training
- **DAVIS-16/17** (unsupervised VOS): Competitive performance without task-specific video training
- Improvement is most dramatic in low-data regimes: >60% relative improvement on rare classes with 10% training data

## Limitations

- End-to-end approaches still outperform DEVA on small-vocabulary benchmarks with abundant video training data (e.g., YouTube-VIS)
- The in-clip consensus introduces latency in the semi-online setting (must wait for a small window of future frames)
- Performance depends on the quality of the chosen image segmentation model — the framework amplifies good image models but cannot fully compensate for poor ones
- The IoU-based merging strategy may struggle with heavy occlusions or drastic appearance changes between the propagated and newly detected segments

## Key takeaways

The critical technical contribution is bi-directional propagation, which combines forward temporal propagation (past context) with in-clip consensus from the image segmentation model (future context). In-clip consensus denoises single-frame segmentation errors by looking at a small clip of nearby frames and reaching agreement, producing more reliable segmentations than any single frame. When new objects appear, the system merges propagated results with fresh in-clip consensus using an IoU-based matching strategy that gracefully handles both existing and newly discovered objects.

DEVA demonstrates that this modular design excels precisely where end-to-end methods struggle: data-scarce and large-vocabulary settings. On rare classes with limited training data, the decoupled approach achieves >60% relative improvement over end-to-end baselines. The framework seamlessly integrates with SAM for open-world segmentation and Grounding-DINO for text-prompted segmentation, showing the flexibility of the decoupled design.

DEVA directly builds on **XMem** as its temporal propagation backbone, inheriting the hierarchical memory architecture but extending it to handle dynamically appearing objects and noisy image segmentations. While XMem assumes a clean initial mask (semi-supervised VOS), DEVA removes this assumption entirely.

**SAM-Track** takes a different approach by combining SAM with DeAOT for interactive segmentation and tracking. DEVA is more general — it works with any image segmenter and focuses on the fusion problem (how to combine per-frame segmentation with temporal propagation), whereas SAM-Track is more focused on interactive user control.

**SAMURAI** (2024) later addresses motion-aware tracking on top of SAM 2, but operates in a different regime — pure visual object tracking rather than the multi-task decoupled segmentation DEVA targets.

The "tracking-by-detection" paradigm is a predecessor, but those approaches treated per-frame detections as immutable. DEVA's in-clip consensus actively denoises image segmentations, allowing the final result to exceed the quality of the image model alone.
