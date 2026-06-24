---
title: "Memory-Centric Embodied Question Answering"
authors: [Mingliang Zhai, Zhi Gao, Yuwei Wu, Yunde Jia]
year: 2025
venue: ""
tags: [embodied-ai, vision-language-models]
url: ""
pdf: "[[2025-memory-centric-eqa.pdf]]"
date_ingested: 2026-06-18
---

# Memory-Centric Embodied Question Answering

![[2025-memory-centric-eqa-thumbnail.png]]

## Research gap

MemoryEQA proposes a memory-centric framework for Embodied Question Answering that positions the memory module as the central hub connecting all system components — planner, stopping module, and answering module — rather than using memory solely for final answer generation as in prior planner-centric approaches. The key insight is that planner-centric methods fail on multi-target tasks because the planner lacks access to historical observations, leading to redundant exploration (e.g., re-searching for objects already found).

## Contributions

- Memory-centric EQA framework that infuses retrieved memory information into all modules (planner, stopping, answering), not just the answering stage
- Viewpoint-contrastive memory update rule combining position/rotation thresholds with structural+semantic similarity to prevent noisy memory corruption
- Entropy-based adaptive retrieval with dynamic k-value selection that adjusts retrieved memory volume based on query complexity
- MT-HM3D benchmark with 1,587 multi-target, multi-region QA pairs specifically testing memory capabilities
- SOTA results on OpenEQA and HM-EQA; 9.9% improvement on MT-HM3D over baselines

## Method

**Memory Storage**: Observations are converted to structured text (decision, objects, caption, position) and encoded via CLIP-ViT-Large into 768-dim features stored in FAISS. Scene memory persists across tasks in the same environment.

**Memory Update**: Triggered only when three conditions are met: (1) agent enters a known region (position and rotation within thresholds βp, βr of existing memories); (2) combined structural (SSIM) and semantic (cosine) similarity exceeds threshold; (3) current observation has sufficient field of view (non-black area > 50%). This prevents both redundant storage and noisy overwrites.

**Memory Retrieval**: Multi-modal query combining initial observation and user question. Entropy-based adaptive thresholds: low-entropy (clear) queries use tight distance thresholds for precise retrieval; high-entropy (ambiguous) queries widen the matching range. Dynamic k-value: k = ⌈kmin + β · Entropy(fq)⌉ — complex queries retrieve more memories.

**Memory-Augmented Planner**: Extends frontier-based exploration with memory guidance. Top-p frontier candidates are evaluated by a VLM using both current observation and retrieved memory, producing relevance scores. The frontier with highest relevance is selected.

**Stopping**: VLM evaluates stopping confidence using current observation + retrieved memory. Stops if confidence exceeds threshold γ.

## Datasets & evaluation

On MT-HM3D: 43.11% success rate with GPT-4o (+9.9% over ExploreEQA baseline of 33.21%), with 36% fewer exploration steps (0.41 vs 0.64 normalized). On HM-EQA: 61.4% (+14% over baseline). On OpenEQA: 30.87 GPT score (SOTA). Ablations show memory retrieval (R) contributes the largest gain (+6.5% on MT-HM3D), followed by dynamic k-value selection (+2.3%). The framework shows positive correlation between VLM capability and overall EQA performance.

## Limitations

- Memory is purely textual (structured text + CLIP features); richer modalities (depth, spatial layout) could improve spatial reasoning
- The entropy-based retrieval heuristic is effective but not learned — whether RL-trained retrieval (as in LMEE) would outperform it is untested
- MT-HM3D questions are VLM-generated and manually verified, but the benchmark's difficulty ceiling may be limited by GPT-4o's question quality
- Scene memory persistence across tasks is mentioned but not evaluated — long-term memory drift and staleness are not addressed
- Performance still relies heavily on VLM quality (positive correlation noted in results), limiting deployment on resource-constrained platforms

## Key takeaways

The framework converts observations into structured textual representations stored in a vector library (FAISS). Three mechanisms govern memory: (1) a viewpoint-contrastive update rule that triggers updates only when the agent enters a known region with sufficiently different visual content, preventing noisy overwrites; (2) an entropy-based adaptive retrieval strategy that adjusts the number of retrieved memories based on query complexity — clear queries retrieve few, precise memories while ambiguous queries cast a wider net; (3) module-specific retrieval queries (qp for planner, qs for stopping, qa for answering) that extract different subsets of memory for each component.

The authors also construct MT-HM3D, a new benchmark of 1,587 QA pairs involving multiple targets across different regions (avg 3.44 objects, 1.49 regions per question), specifically designed to evaluate memory capabilities. MemoryEQA achieves 43.11% success rate on MT-HM3D (+9.9% over ExploreEQA baseline), 61.4% on HM-EQA (+14% over baseline), and SOTA on OpenEQA.

MemoryEQA directly addresses the limitation identified by LMEE/MemoryExplorer — that memory should guide exploration, not just post-hoc answering. Where LMEE uses RL to train active memory retrieval behavior, MemoryEQA achieves this through engineered mechanisms (entropy-based retrieval, viewpoint-contrastive updates) without task-specific training, making it more immediately deployable.

Compared to ReMEmbR's flat vector database retrieval, MemoryEQA adds structured textual representations and module-specific retrieval queries. The entropy-based adaptive retrieval is a principled alternative to STaR's Information Bottleneck approach — both address the "how much to retrieve" problem, but MemoryEQA uses query-side entropy while STaR uses memory-side redundancy.

The viewpoint-contrastive update mechanism relates to EmbodiedLGR's deduplication via spatial/semantic thresholds, but operates at the observation level rather than the graph node level. The frontier-based planner augmented with memory guidance is a lighter-weight version of Mind Palace's VoI-based explore-vs-recall trade-off.
