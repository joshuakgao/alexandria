---
title: "ReMEmbR: Building and Reasoning Over Long-Horizon Spatio-Temporal Memory for Robot Navigation"
authors: [Abrar Anwar, John Welsh, Joydeep Biswas, Soha Pouya, Yan Chang]
year: 2024
venue: "CoRL 2024"
tags: [embodied-ai, video-understanding, agentic-mllms]
url: ""
pdf: "[[2024-remembr.pdf]]"
date_ingested: 2026-06-18
---

# ReMEmbR: Building and Reasoning Over Long-Horizon Spatio-Temporal Memory for Robot Navigation

![[2024-remembr-thumbnail.png]]

## Research gap

ReMEmbR (Retrieval-augmented Memory for Embodied Robots) addresses the problem of long-horizon video question answering for robot navigation. Robots deployed for extended periods accumulate histories that grow unboundedly—too large for any fixed context window. ReMEmbR decomposes the problem into two phases: memory building and querying, using a vector database as the core memory representation.

## Contributions

- Introduces ReMEmbR, an LLM-agent with iterative retrieval over a vector database for long-horizon embodied question answering
- Proposes the NaVQA dataset with spatial, temporal, and descriptive questions over long robot navigation videos
- Demonstrates that retrieval-based approaches scale better than context-window-based methods (VLM/LLM) for long histories
- Provides real-world robot deployment showing the system handles diverse natural language queries
- Formulates spatio-temporal QA with actionable outputs (metric coordinates, timestamps) rather than just text

## Method

**Memory Building**: Video frames are aggregated into t-second segments, captioned by VILA-13b at 2 FPS, embedded with mxbai-embed-large-v1, and stored in a vector database alongside GPS coordinates and timestamps. This runs incrementally as the robot navigates.

**Querying**: An LLM-agent iteratively queries the vector database using three retrieval functions: text retrieval (by semantic similarity), position retrieval (by spatial proximity), and time retrieval (by temporal proximity). The LLM can issue up to k queries per iteration, receiving m memories per query. After each round, it decides whether to answer or retrieve more. The final answer is formatted as structured JSON with text, position, time, or duration fields.

The approach is formalized as finding a minimal subset R* of the full history H that preserves answer correctness: R* = argmin|R| s.t. the answer from R matches the answer from H.

## Datasets & evaluation

- ReMEmbR with GPT-4o outperforms all baselines on long-horizon videos (>7 min) across descriptive accuracy, positional error, and temporal error
- Multi-frame VLM (GPT-4o) cannot process medium or long videos due to context limits; ReMEmbR scales gracefully
- ReMEmbR maintains higher overall correctness as video length increases, unlike baselines which degrade
- Low latency: ~12 seconds for a 21.5-minute video, compared to context-window methods that scale linearly with history length
- Open-source LLMs (Codestral, Command-R, Llama3.1-8b) work with ReMEmbR but with significantly lower performance than GPT-4o

## Limitations

- Evaluation limited to 210 questions across 7 sequences; broader benchmarking needed
- Relies heavily on caption quality from VILA; captioning errors propagate through the entire pipeline
- Vector database retrieval uses approximate nearest neighbor, which may miss relevant memories for complex multi-hop queries
- The iterative querying loop adds latency proportional to the number of retrieval steps, though still faster than processing full histories
- No mechanism for memory consolidation or forgetting—the database grows unboundedly
- Temporal reasoning accuracy degrades with longer time horizons

## Key takeaways

During memory building, the system processes consecutive video segments through VILA (a video captioning model), embeds the captions using mxbai-embed-large-v1, and stores the embeddings alongside position coordinates and timestamps in a vector database. This runs in real-time as the robot operates.

During querying, an LLM-agent acts as a state machine that iteratively formulates retrieval calls—text-based, spatial, or temporal—against the vector database. At each step, the LLM decides what to query, evaluates whether enough context has been retrieved to answer the question, and either retrieves more memories or generates the final answer. This iterative retrieval loop handles three query types: spatial ("Where is the closest bathroom?" → (x,y) coordinates), temporal ("When did you see the boxes fall?" → "15 minutes ago"), and descriptive ("Was the sidewalk busy today?" → yes/no or free text).

The paper also introduces NaVQA, a dataset of 210 annotated questions across 7 long-horizon robot navigation videos (15–30 minutes each) from the CODa urban navigation dataset.

ReMEmbR extends embodied question answering (EQA) beyond short 30-second windows (as in OpenEQA) to arbitrary-length robot histories. Unlike MobilityVLA which stuffs entire histories into Gemini's 1M context window, ReMEmbR uses retrieval to scale independently of context length. Compared to scene graphs and topological memory approaches (e.g., Hydra), ReMEmbR captures dynamic events and temporal information that static spatial representations miss. Within this wiki, ReMEmbR complements [[2025-embodied-rag|Embodied-RAG]]—both use retrieval over embodied experiences, but ReMEmbR emphasizes temporal reasoning and iterative LLM-agent querying while Embodied-RAG focuses on hierarchical spatial abstraction via semantic forests.
