---
title: "Embodied-RAG: General Non-parametric Embodied Memory for Retrieval and Generation"
authors: [Quanting Xie, So Yeon Min, Pengliang Ji, Yue Yang, Tianyi Zhang, Kedi Xu, Aarav Bajaj, Ruslan Salakhutdinov, Matthew Johnson-Roberson, Yonatan Bisk]
year: 2025
tags: [embodied-memory, scene-graphs]
url: ""
pdf: "[[2025-embodied-rag.pdf]]"
date_ingested: 2026-06-18
---

# Embodied-RAG: General Non-parametric Embodied Memory for Retrieval and Generation

![[2025-embodied-rag-thumbnail.png]]

## Research gap

Embodied-RAG adapts Retrieval-Augmented Generation (RAG) from NLP to the embodied robotics domain, addressing the fundamental mismatch between text-oriented RAG systems and multimodal, spatially-grounded robot experiences. The core insight is that embodied data—tuples of timestamps, sensor observations, and poses—are redundant, hierarchically correlated, and spatially grounded, making naive text-based RAG approaches ineffective.

## Contributions

- Extends RAG to embodied settings, identifying three core challenges: multimodal representation, structural awareness, and observation redundancy
- Introduces the semantic forest—a hierarchical memory structure built via spatial-semantic clustering with LLM summarization at each level
- Proposes a top-down retrieval mechanism using parallelized LLM-guided tree traversals, replacing pure semantic similarity with structured reasoning
- Presents the Embodied-Experiences Dataset spanning 19 diverse environments with 250+ annotated queries
- Demonstrates platform-agnostic operation across drones, quadrupeds, and wheeled robots

## Method

**Bottom-up Memory Building** starts with a topological graph where each node stores pose, images, timestamps, and VLM-generated captions. Nodes are hierarchically clustered using complete-linkage clustering (CLINK) with a hybrid similarity metric combining spatial similarity (haversine distance with exponential decay) and semantic similarity (cosine similarity of text embeddings). At each level, an LLM generates summaries for each cluster, creating progressively more abstract descriptions.

**Top-down Retrieval** operates in two phases: (1) semantic-guided hierarchical traversal using an LLM selection function that prunes irrelevant branches at each tree level, and (2) hybrid re-ranking of collected base nodes combining LLM relevance scores with spatial proximity (conditioned on agent location) and optional sensor data.

**Generation** passes retrieved nodes as context to an LLM that either outputs a navigation waypoint (for "find" queries, with Dijkstra pathfinding on the topological graph) or a textual explanation (for "explain" queries).

## Datasets & evaluation

- Outperforms Naive RAG, GraphRAG, and LightRAG across explicit, implicit, and global query types
- Memory building is 7.38× faster than GraphRAG and 9.76× faster than LightRAG
- Successfully operates on kilometer-scale environments (3,525-node street-view graph)
- Demonstrated on multiple robotic platforms including Unitree Go2 quadruped
- Multimodal sensor integration (NDVI) further improves retrieval for domain-specific queries

## Limitations

- Reliance on VLM (GPT-4o) for captioning and LLM for summarization/retrieval introduces latency and cost at scale
- The hybrid distance metric weighting (α) between spatial and semantic similarity requires tuning per environment type
- Evaluation is primarily on static environments; dynamic scene changes and temporal memory updates are not addressed
- The approach assumes pre-collected topological graphs; online incremental building during exploration is mentioned but not fully evaluated
- Cross-environment transfer and generalization of learned retrieval strategies remain unexplored

## Key takeaways

The system introduces a two-stage architecture: bottom-up memory building and top-down retrieval. Memory is structured as a **semantic forest**, a hierarchical tree built by clustering topological map nodes using a hybrid spatial-semantic distance metric, then generating LLM summaries at each level. This produces a multi-resolution representation ranging from fine-grained object descriptions at leaf nodes to holistic area summaries at root nodes.

For retrieval, Embodied-RAG uses parallelized tree traversals inspired by Tree-of-Thoughts, with an LLM selecting the most relevant branches at each level. A hybrid re-ranking phase combines semantic and spatial scores, optionally incorporating additional sensor data (e.g., NDVI for vegetation quality). The system handles three query types: explicit ("find the stairs"), implicit ("where can I buy drinks?"), and global ("describe the function of this building").

Evaluated on 250+ queries across 19 real and simulated environments (including kilometer-scale outdoor scenes), Embodied-RAG outperforms Naive RAG, GraphRAG, and LightRAG on all query types while being 7.38× faster than GraphRAG and 9.76× faster than LightRAG for memory construction.

Embodied-RAG positions itself against three research threads: (1) text-based RAG systems (Naive RAG, GraphRAG, LightRAG) which lack spatial grounding; (2) semantic metric maps and 3D scene graphs (e.g., Hydra) which rely on human-engineered schemas and don't scale to diverse outdoor environments; and (3) embodied question answering systems which are limited to indoor settings or lack active navigation. The semantic forest can be seen as an automatic, schema-free alternative to structured scene graphs like Hydra, trading hand-designed room/object hierarchies for data-driven spatial-semantic clustering.
