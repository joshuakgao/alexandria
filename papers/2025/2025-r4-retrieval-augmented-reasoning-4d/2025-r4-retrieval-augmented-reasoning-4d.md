---
title: "R⁴: Retrieval-Augmented Reasoning for Vision-Language Models in 4D Spatio-Temporal Space"
authors: [Kihyun Sohn, Kurt Dillitzer, Jason Corso, Alexander Sax]
year: 2025
tags: [embodied-memory, vision-language-models, slam]
url: ""
pdf: "[[2025-r4-retrieval-augmented-reasoning-4d.pdf]]"
date_ingested: 2026-06-18
---

# R⁴: Retrieval-Augmented Reasoning for Vision-Language Models in 4D Spatio-Temporal Space

![[2025-r4-retrieval-augmented-reasoning-4d-thumbnail.png]]

## Research gap

R⁴ introduces a training-free framework that equips vision-language models (VLMs) with structured, lifelong 4D memory for retrieval-augmented reasoning. The core idea is to continuously build a knowledge database where every observed object is stored as a structured JSON record with three fields: SEM (semantic description), SPA (3D centroid and bounding-box extent in global SLAM coordinates), and TEM (appearance/disappearance timestamps). This creates a persistent, metric-anchored world model that grows over time and can be shared across multiple agents.

## Contributions

- Introduces a continuous 4D knowledge database that incrementally stores object-level features (semantic + spatial + temporal) anchored in a globally consistent SLAM map
- Proposes retrieval-augmented 4D reasoning with structured decomposition of queries into semantic, spatial, and temporal keys
- Demonstrates multi-agent collaborative memory through shared SLAM maps, enabling agents to inherit and extend each other's observations
- Achieves SOTA on three embodied reasoning benchmarks with a small 4B VLM, outperforming GPT-5 and o3 on ERQA
- Training-free: no fine-tuning of the VLM backbone, all reasoning is prompt-driven

## Method

**Storage Pipeline**: At each timestep, RGB images and point clouds are processed by SAM2 for object segmentation. For each detected object, a 3D centroid and bounding-box extent are computed from the point cloud, a VLM generates a semantic description, and timestamps are recorded. Each object becomes a JSON record {SEM, SPA, TEM} stored in three complementary backends: a vector database (semantic embeddings), a metric Euclidean space (spatial features via SLAM), and a columnar timeseries database (temporal intervals). New observations are matched against existing entries using spatial distance and semantic similarity thresholds; matches trigger updates, otherwise new entries are created.

**Retrieval-Reasoning Pipeline**: Given a query, the VLM first assesses whether it can answer from live perception (self-estimated answerability). If not, it decomposes the query into semantic, spatial, and/or temporal keys. These keys query the 4D database along their respective axes, returning complete 4D records. Retrieved records are serialized into textual context and fed back to the VLM for reasoning. The loop can iterate, with each reasoning output becoming the next query input, enabling multi-hop reasoning (e.g., find a table, then query what's near it).

**Collaborative Enrichment**: Multiple agents share a common database by aligning their SLAM maps into a shared global frame, enabling cross-agent observation sharing and refinement.

## Datasets & evaluation

- **ERQA**: 70.25% accuracy, surpassing GPT-5 (65.7%) and o3 (64.0%) despite using only Gemma3-4B
- **OpenEQA EM-EQA**: Approaches human baseline (-7.03%), outperforming GPT-4V by +15.37% overall and +30.36% on HM3D
- **OpenEQA A-EQA**: Best results on active exploration tasks, demonstrating generalization beyond static recall
- **VLM4D**: SOTA on 4D spatio-temporal reasoning tasks
- Ablation shows all three retrieval axes (semantic, spatial, temporal) contribute, with spatial retrieval providing the largest individual gain

## Limitations

- Relies on SAM2 segmentation quality; missed or over-segmented objects corrupt the database
- SLAM backend (MapAnything) must provide globally consistent coordinates; SLAM failures cascade into spatial retrieval errors
- Object matching thresholds (ϵ_c, δ_s) require tuning per environment; robustness to threshold sensitivity is not thoroughly analyzed
- Semantic descriptions from the VLM can be noisy or inconsistent across viewpoints; no explicit mechanism for resolving contradictory descriptions
- Scalability of the iterative retrieval-reasoning loop to very large databases (millions of objects over days of operation) is not evaluated
- Multi-agent experiments are limited; real-world multi-robot coordination with shared memory remains to be demonstrated at scale

## Key takeaways

At inference, natural language queries are decomposed into semantic, spatial, and temporal retrieval keys. Unlike standard RAG which retrieves text passages, R⁴ retrieves directly from 4D space—combining cosine similarity over language embeddings (semantic), nearest-neighbor search in Euclidean SLAM coordinates (spatial), and interval matching over timestamps (temporal). The system features a self-estimated answerability check: the VLM first attempts to answer from live perception alone, and only enters the retrieval-reasoning loop if it cannot answer confidently.

R⁴ achieves state-of-the-art results on ERQA (70.25%, surpassing GPT-5 at 65.7%), OpenEQA (approaching human-level on episodic memory tasks), and VLM4D benchmarks—all using only a 4B-parameter Gemma3 backbone without any task-specific training.

R⁴ explicitly builds on and extends both [[2025-embodied-rag|Embodied-RAG]] and [[2024-remembr|ReMEmbR]]. Like Embodied-RAG, it maintains a structured spatial memory, but R⁴ anchors objects in metric SLAM coordinates rather than topological graphs, enabling precise geometric reasoning. Like ReMEmbR, it uses iterative retrieval with semantic/spatial/temporal queries, but R⁴ stores structured 4D records per object rather than video caption embeddings in a flat vector database. R⁴ also introduces multi-agent collaborative memory, which neither prior work addresses. The paper positions itself against 3D scene graphs (e.g., Hydra) and Mind Palace approaches, arguing that its continuous 4D database with retrieval-reasoning loops provides more flexible querying than schema-dependent representations.
