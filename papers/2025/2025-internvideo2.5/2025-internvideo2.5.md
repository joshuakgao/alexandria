---
title: "InternVideo2.5: Empowering Video MLLMs with Long and Rich Context Modeling"
authors: [Yi Wang, Xinhao Li, Ziang Yan, Yinan He, Jiashuo Yu, Xiangyu Zeng, Chenting Wang, Changlian Ma, Haian Huang, Jianfei Gao, Min Dou, Kai Chen, Wenhai Wang, Yu Qiao, Yali Wang, Limin Wang]
year: 2025
venue: ""
tags: [video-understanding]
url: ""
pdf: "[[2025-internvideo2.5.pdf]]"
date_ingested: 2026-06-18
---

# InternVideo2.5: Empowering Video MLLMs with Long and Rich Context Modeling

![[2025-internvideo2.5-thumbnail.png]]

## Research gap

InternVideo2.5 addresses a key limitation of video MLLMs: their inability to handle long videos while preserving fine-grained details. The paper proposes two complementary mechanisms to enhance context modeling: (1) **Hierarchical Token Compression (HiCo)** which adaptively compresses visual and semantic tokens both visually and hierarchically, enabling processing of videos 6+ times longer than standard models; (2) **Task Preference Optimization (TPO)** which uses direct preference optimization to transfer dense vision annotations into the MLLM, improving fine-grained perception. The approach is learned in three stages using both short and long video data plus classical vision task annotations. Results show significant improvements on short-form (MVBench, VideoMME) and long-form benchmarks, plus emergent capabilities in object tracking and segmentation without explicit task-specific training.

## Contributions

- First comprehensive study on long and rich context (LRC) modeling for video MLLMs, unifying token compression and task preference optimization
- Hierarchical token compression approach enabling 6x+ longer video memory without substantial performance loss
- Task preference optimization using dense annotations (segmentation, tracking) to improve fine-grained visual perception
- SOTA results on multiple video understanding benchmarks (short and long) plus emergent vision perception abilities
- Demonstration that context richness (length + fineness) is orthogonal to model scaling and provides efficient improvement axis

## Method

**Hierarchical Token Compression (HiCo):** Uses two complementary token reduction strategies—visual compression (removing redundant spatial tokens) and semantic compression (pooling similar semantic tokens across time). Applies Token Merging (ToMe) and temporal dropping (TDrop) adaptively based on video length to maintain a fixed computational budget.

**Task Preference Optimization (TPO):** Transfers knowledge from specialized vision models (for segmentation, tracking, etc.) into the MLLM via direct preference optimization. Uses vision expert models as preference annotators to create high-quality training signals for fine-grained tasks without requiring downstream-task-specific modules.

**Three-stage training:** (1) Supervised fine-tuning on short video QA; (2) long video instruction tuning with compressed tokens; (3) task preference optimization with dense vision annotations.

## Datasets & evaluation

- **Short videos:** InternVideo2.5-8B achieves 74.0% on MVBench, 64.2% on VideoMME (competitive with larger models)
- **Long videos:** Extends memory to 6+ times original capacity while maintaining or improving accuracy
- **Fine-grained tasks:** Enables object tracking, segmentation, and temporal localization without task-specific decoders
- **Efficiency:** Maintains computational efficiency while handling longer sequences and richer annotations

## Limitations

- How well does hierarchical compression transfer to other domains (e.g., surveillance, egocentric video)?
- Can TPO be extended to other vision tasks beyond tracking and segmentation?
- Trade-off between token compression rate and task-specific performance remains underexplored for very sparse annotations
- Generalization of learned compression patterns to videos with very different frame rates, resolutions, or motion characteristics

## Key takeaways

Extends prior Video-LLM architectures by addressing two orthogonal bottlenecks: (1) context length (vs. prior work on expanding context windows); (2) context fineness (vs. prior work on general-purpose scaling). Complements other long-video approaches like agent-based methods (which decompose tasks) and specialized temporal models (which target specific phenomena). InternVideo2.5 shows that token compression + annotation transfer is a practical, scalable path to richer understanding.
