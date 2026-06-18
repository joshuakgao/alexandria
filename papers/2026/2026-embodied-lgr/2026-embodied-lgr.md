---
title: "EmbodiedLGR: Integrating Lightweight Graph Representation and Retrieval for Semantic-Spatial Memory in Robotic Agents"
authors: [Paolo Riva, Leonardo Gargani, Matteo Frosi, Matteo Matteucci]
year: 2026
tags: [embodied-memory]
url: ""
pdf: "[[2026-embodied-lgr.pdf]]"
date_ingested: 2026-06-18
---

# EmbodiedLGR: Integrating Lightweight Graph Representation and Retrieval for Semantic-Spatial Memory in Robotic Agents

![[2026-embodied-lgr-thumbnail.png]]

## Research gap

EmbodiedLGR-Agent focuses on an underexplored dimension of embodied memory: **efficiency and real-time responsiveness**. While prior systems (ReMEmbR, Embodied-RAG, R⁴) prioritize accuracy and reasoning capability, they rely on computationally expensive models and monotonically growing, redundant memory stores that hinder real-time human-robot interaction. EmbodiedLGR addresses this by proposing a hybrid dual-level memory architecture with a lightweight processing pipeline designed for edge deployment.

## Contributions

- Proposes a hybrid dual-level memory: semantic memory graph for low-level object tracking + vector database for high-level scene descriptions
- Addresses memory redundancy via real-time node updates using spatial (δ_P) and semantic (δ_E) similarity thresholds for entity deduplication
- Achieves SOTA inference speed using Florence-2 (<1B parameters) instead of 7B–13B VLMs, enabling edge deployment
- Defines three specialized on-graph retrieval tools (semantic, positional, temporal) for low-latency querying
- Demonstrates full on-robot deployment with local VLM inference during both exploration and querying phases

## Method

**Memory Building**: Image frames are processed by Florence-2 using DENSE-REGION-CAPTION (object labels → graph nodes) and MORE-DETAILED-CAPTION (scene descriptions → vector DB entries). Each graph node stores {embedded_label, pose, time}. When an entity is re-observed within spatial threshold δ_P (5m) and semantic threshold δ_E (0.75 cosine similarity), the existing node is updated via moving average on position rather than creating a duplicate. Multiple objects of the same type are handled by comparing observed count vs. stored count per label type.

**Vector Database**: Scene-level descriptions are embedded via all-MiniLM-L6-v2 and stored as {description_embedding, pose, time} triplets in Milvus.

**Retrieval**: Three graph tools—semantic search (cosine similarity on label embeddings), positional search (L2 distance on coordinates), temporal search (L1 distance on timestamps)—each return k nearest nodes. For complex queries, the LLM falls back to a ReMEmbR agent querying the vector database. The LLM enters a reasoning loop, selecting tools based on query complexity until it can answer.

## Datasets & evaluation

- **Response latency**: Graph-only queries ~10s vs. ~20s for vector DB (ReMEmbR), ~50% reduction
- **Accuracy**: Florence-2-large with Graph+ReMEmbR achieves 33.90% average accuracy (vs. 29.11% ReMEmbR-only), with 29.17% positional accuracy (vs. 19.59%)
- **Edge deployment**: Florence-2-base (0.22B) and Florence-2-large (0.77B) run locally on robot hardware
- **Fallback behavior**: Florence-2-large configuration falls back to vector DB for ~47% of queries (vs. ~93% for Florence-2-base), indicating better graph-level object labels from the larger model
- Graph memory improves positional accuracy significantly due to fine-grained per-object pose storage vs. per-frame pose in vector DB

## Limitations

- Accuracy (33.90%) is significantly lower than R⁴ or Mind Palace, reflecting the cost of lightweight VLMs—Florence-2 misses objects and produces less precise labels
- The 25m spatial accuracy threshold and 3-minute temporal threshold are generous compared to other benchmarks
- Memory graph structure (NetworkX) may not scale efficiently to very large environments with thousands of objects
- Entity deduplication thresholds (δ_P, δ_E) are manually tuned and environment-specific; robustness to threshold sensitivity is not analyzed
- No temporal reasoning about object changes over time—the node update overwrites previous positions rather than preserving history
- Evaluated only on NaVQA; comparison with R⁴'s benchmarks (ERQA, OpenEQA) would better contextualize accuracy trade-offs

## Key takeaways

The system uses **Florence-2** (0.22B–0.77B parameters)—dramatically smaller than VILA-13B or Qwen2.5-VL-7B used by prior work—to extract both object labels (populating a semantic memory graph) and scene captions (populating a vector database). The **memory graph** stores low-level object information as nodes with embedded labels, positions, and timestamps, supporting real-time updates when the same entity is re-observed (via spatial distance and semantic similarity thresholds). The **vector database** retains richer scene-level descriptions for semantically complex queries. This dual-level separation lets the LLM agent route queries to the most efficient structure: fast graph traversal for simple object/position/time queries, falling back to vector DB retrieval only for complex semantic queries.

Three on-graph retrieval tools (semantic, positional, temporal search) enable direct graph querying with low latency, while a ReMEmbR agent handles vector DB queries as a fallback. Evaluated on NaVQA, EmbodiedLGR achieves SOTA inference and querying times (graph-only response latency ~10s vs. ~20s for vector DB) while maintaining competitive accuracy. The system was successfully deployed on a physical robot running the entire VLM and memory pipeline locally.

EmbodiedLGR directly extends [[2024-remembr|ReMEmbR]], using a ReMEmbR agent as the vector DB fallback while adding the graph memory layer for efficiency. The dual-level approach echoes [[2025-r4-retrieval-augmented-reasoning-4d|R⁴]]'s separation of semantic, spatial, and temporal backends, but EmbodiedLGR prioritizes lightweight deployment (Florence-2 at <1B) over R⁴'s heavier pipeline (SAM2 + SLAM + Gemma3-4B). The entity deduplication via node updates addresses the redundancy problem that [[2025-embodied-rag|Embodied-RAG]] handles through hierarchical clustering. Compared to all other systems in this topic, EmbodiedLGR is the most deployment-focused: it's the only paper that runs the entire VLM + memory pipeline locally on robot hardware, making it the most practically constrained but also the most realistic for real-time HRI scenarios.
