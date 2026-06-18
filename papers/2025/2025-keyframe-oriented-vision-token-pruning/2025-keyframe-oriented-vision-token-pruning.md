---
title: "Keyframe-oriented Vision Token Pruning: Enhancing Efficiency of Large Vision Language Models on Long-Form Video Processing"
authors: [Yudong Liu, Jingwei Sun, Yueqian Lin, Jianyi Zhang, Ming Yin, Qinsi Wang, Guangzhi Su, Hai Li]
year: 2025
tags: [video-understanding, vision-language-models]
url: ""
pdf: "[[2025-keyframe-oriented-vision-token-pruning.pdf]]"
date_ingested: 2026-06-18
---

# Keyframe-oriented Vision Token Pruning: Enhancing Efficiency of Large Vision Language Models on Long-Form Video Processing

![[2025-keyframe-oriented-vision-token-pruning-thumbnail.png]]

## Research gap

KVTP (Keyframe-oriented Vision Token Pruning) addresses the computational overhead of processing long-form videos with vision language models. Unlike prior approaches that either indiscriminately prune vision tokens (losing spatio-temporal structure) or select keyframes by discarding others (disrupting context), KVTP combines both strategies through adaptive, query-aware pruning rates. A frame relevance predictor estimates each frame's importance relative to the query, then assigns per-frame pruning rates that retain rich token details from keyframes while aggressively pruning less relevant frames. This preserves temporal and contextual continuity while achieving 80% token reduction without performance loss.

## Contributions

- **Hybrid pruning-selection framework**: bridges the gap between naive token pruning (insensitive to frame importance) and binary keyframe selection (disrupts context); soft pruning rates adapt to frame relevance
- **Query-aware frame relevance prediction**: frame importance scoring depends on both video content and the specific query, not just intrinsic frame properties
- **80% token reduction with performance preservation**: achieves substantial efficiency gains on long-form video QA without degrading spatiotemporal reasoning or answer accuracy
- **SparseKV-QA benchmark**: unified benchmark from seven datasets (VideoMME, EgoSchema, NeXT-QA, etc.) highlighting real-world sparse key information scenarios; fills gap in existing benchmarks which do not explicitly focus on sparse-event videos
- **Scalability demonstration**: evaluated on VLMs of varying sizes (7B to 72B parameters), showing consistent gains across model scales

## Method

**Frame relevance prediction:** A lightweight module predicts query-frame relevance scores during inference, considering both the query tokens and frame visual content. The prediction is query-dependent; the same frame receives different scores for different queries.

**Soft pruning mechanism:** Rather than binary selection (keep/discard), a soft pruning rate mechanism assigns each frame a pruning percentage. Keyframes (high relevance) retain more tokens; low-relevance frames have more tokens pruned. This is implemented via learned masking/downsampling of the frame's token sequence.

**Pipeline integration:** Frames are encoded into vision tokens using a vision encoder. Before passing to the LLM backbone, the adaptive pruning rates are applied per-frame, reducing the total sequence length while preserving the structure of important frames.

## Datasets & evaluation

**Main benchmark (SparseKV-QA):** KVTP achieves 80% token reduction (processed tokens reduced from 100% to 20%) while maintaining baseline accuracy across all VLMs tested. Performance versus FLOPs curves show KVTP substantially outperforms comparisons (PruMerge, random sampling, ToMe) across multiple model sizes.

**Generalization:** Tested on multiple VLMs (LLaVA-variants, Video-LLaVA, etc.) and consistently reduces token usage without accuracy drop, demonstrating robustness to model architecture.

**Comparison to alternatives:**
- vs. Token merging (ToMe): ToMe indiscriminately merges similar tokens; KVTP's query-aware approach is superior
- vs. Random sampling: deterministic frame-based pruning outperforms random token removal
- vs. PruMerge: baseline token pruning without query-awareness

## Limitations

- **Relevance prediction accuracy:** no analysis of how prediction errors affect downstream performance; if relevance scores are miscalibrated, pruning could be suboptimal
- **Generalization to novel query types:** evaluated on QA; unclear how well soft pruning rates transfer to other tasks (grounding, captioning, summarization)
- **Cross-dataset transfer:** does relevance prediction trained on one dataset generalize to held-out datasets with different video distributions?
- **Interactive/streaming scenarios:** offline relevance prediction assumes full video is available; real-time streaming scenarios may require adaptive pruning
- **Frame dependencies:** pruning is per-frame; no explicit modeling of frame dependencies; whether temporal context between frames is preserved during aggressive pruning is unclear
- **Multi-query scenarios:** each frame's pruning rate is query-specific; if a single video is queried multiple times with different queries, computational savings may diminish

## Key takeaways

The paper also introduces SparseKV-QA, a benchmark constructed from seven existing datasets, designed to evaluate token compression methods on realistic scenarios where videos contain sparse but crucial keyframes interspersed with long stretches of irrelevant content.

**Complements temporal search approaches:**
- [[papers/2025-timesearch-r-temporal-search-video-understanding|TimeSearch-R]] learns to select *which frames to process* via RL; KVTP decides *how many tokens to keep* from each frame. They are orthogonal: KVTP could enhance TimeSearch-R by reducing token overhead even further.
- [[papers/2025-agentic-keyframe-search|AKEYS]] uses language agents to navigate keyframe trees; KVTP provides a fine-grained token-level efficiency mechanism complementary to frame selection.
- [[papers/2025-re-thinking-temporal-search-long-form-video|T*]] reformulates temporal search as spatial search; KVTP is a token-efficiency orthogonal to T*'s search strategy.

**Relates to architecture efficiency:**
- [[papers/2026-videoweave|VideoWeave]] optimizes through data composition; KVTP optimizes through per-frame token allocation. Both are complementary efficiency axes.

**Addresses overview bottleneck:** The overview identifies "computational cost at deployment scale" as an open problem; KVTP directly tackles this through token reduction.
