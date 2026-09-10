---
title: "FARM: Find Anything using Relational Spatial Memory"
authors: [Siming He, Leo Huang, Adam Lilja, Fabio Hübel, Jonas Frey, Marco Pavone, S. Shankar Sastry, Jitendra Malik, Claire Tomlin]
year: 2026
tags: [embodied-ai, 3d-scene-understanding]
url: "https://arxiv.org/abs/2606.15476"
date_ingested: 2026-08-01
---

# FARM: Find Anything using Relational Spatial Memory

![[2026-farm-find-anything-relational-spatial-memory-thumbnail.png]]

## Research gap
Robots in object-rich environments need to retrieve specific object instances from relational language queries (e.g., "the tall lamp below the dartboard and to the left of the poster"), but existing systems fall short. Closed-vocabulary scene graphs lack open-vocabulary flexibility; open-vocabulary scene graphs require hyperparameter tuning across scene scales and offline post-processing; end-to-end VLM reasoning over frame histories cannot scale to the thousands of viewpoints accumulated in large environments; and embedding-based retrieval (e.g., CLIP) fails to capture relational and spatial predicates among multiple objects.

## Contributions
- A real-time (5–10 Hz) online scene-graph construction algorithm that scales from 15 m² rooms to 15,000 m² outdoor environments under one fixed hyperparameter configuration, with no offline post-processing.
- A relational retrieval framework that parses queries into executable symbolic specifications with soft spatial predicate evaluators, achieving higher accuracy and top-K recall than both embedding-only and end-to-end VLM reasoning approaches.
- FARM-Scenes, a benchmark of seven large-scale indoor and outdoor scenes (1,800–15,000 m²) for relational object grounding at scale.
- Closed-loop deployment on a Boston Dynamics Spot robot with onboard RGB-D sensing and NVIDIA Jetson Thor compute.

## Method
**Memory construction**: Each object is represented as a single 3D Gaussian with accumulated attributes: geometry (mean + covariance), detection-time appearance features, up to k representative views selected for viewpoint diversity, an open-vocabulary caption from those views, and three retrieval embeddings (Qwen3-text caption, SigLIP2 image, Qwen3-VL image). Relations stored are covisibility edges and spatial adjacency via Hellinger distance — higher-order relations are computed on demand at query time. The synchronous loop (detect via YOLOE, lift with depth, associate via geometry + semantic filtering, fuse) is GPU-vectorized. VLM captioning and embedding run asynchronously off the critical path via vLLM workers.

**Relational retrieval** proceeds in three stages:
1. **Parse**: Qwen3.5-9B compiles the query into a typed query graph — target variable, anchor variables, descriptions, and spatial/semantic predicates.
2. **Score**: Each candidate target is scored by soft unary evaluators (reciprocal rank fusion over three embeddings) and relational evaluators (closed-form functions over Gaussian geometry for Near, LeftOf, Above, etc.). Star-shaped query decomposition avoids exhaustive joint enumeration.
3. **Rerank**: Top-5 candidates are verified by a Qwen3.5-9B VLM inspecting rendered mask overlays in stored viewpoints.

## Datasets & evaluation
- **Benchmarks**: ScanNet (30 scenes), HM3D (30 scenes), FARM-Scenes (7 large-scale scenes); 44k language queries total spanning 67 scenes from 15 to 15,000 m².
- **Metrics**: Accuracy@1, Recall@5, Recall@10, MRR, using visible-mask IoU.
- **Results on ScanNet-30**: A@1 35.9% (vs. 29.3% DAAAM, 28.0% RynnBrain, 15.4% BBQ); R@10 74.6% (vs. 23.5% BBQ) — 3.2× improvement.
- **Results on HM3D-30**: A@1 7.9% (vs. 6.1% DAAAM, 5.3% BBQ); R@10 26.9% (vs. 12.8% BBQ) — 2.1× improvement.
- **FARM-Scenes**: A@1 24.2% (vs. 15.1% DAAAM+R, 7.4% BBQ); R@10 47.3%.
- **Efficiency**: Mapping at ~8 Hz, memory ~23 MiB (ScanNet), query latency 1.7–2.1 s without reranking.
- **Ablations**: Locked retrieval (embedding fusion + soft predicates) beats both pure cosine similarity and BBQ's LLM retrieval. End-to-end VLM reasoning can actually hurt performance vs. strong embedding-only retrieval.

## Limitations
- Spatial predicates are manually specified with fixed parameters (not learned or calibrated); e.g., Near decays too quickly with distance in some cases.
- Uniform weighting across semantic and spatial scores can allow semantically similar distractors to dominate even when spatial constraints favor the true target.
- Current implementation handles relations between target and anchors but not compositional reasoning among anchors (e.g., "a sofa that a humanoid robot sits on" as an anchor specification).
- Evaluated primarily on referring-expression benchmarks; more complex multi-hop relational queries remain untested.

## Key takeaways
- Structured symbolic retrieval over spatial memory (parse → soft predicate scoring → VLM reranking) substantially outperforms both end-to-end VLM reasoning over frames and embedding-only retrieval for relational object grounding.
- End-to-end VLM reasoning can actually hurt performance relative to strong embedding-only retrieval on spatial queries, supporting the case for explicit spatial predicate evaluation.
- A single fixed hyperparameter configuration can scale from small indoor rooms to 15,000 m² outdoor environments, with stable per-frame mapping latency as trajectory length grows.
- Multi-embedding fusion (text caption + image embeddings from two models) provides complementary retrieval signals; no single embedding dominates across all scene types.
- Asynchronous VLM captioning off the critical mapping path enables real-time construction without sacrificing the richness of open-vocabulary object descriptions.
