---
topic: Quantization
slug: quantization
---

# Quantization

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "quantization")
SORT year DESC
```

## Overview

*Last updated: 2026-04-12 | Sources: 1 paper*

## Current thesis

Quantization enables efficient deployment of large-scale models by reducing numerical precision while preserving model performance. Current work emphasizes theoretically-grounded methods with information-theoretic bounds, particularly for vector quantization in KV cache compression. The field moves toward online, accelerator-friendly algorithms that can be deployed in real-time inference without requiring offline preprocessing.

## Key trends

**2025**: Emergence of theoretically-optimal vector quantization methods (TurboQuant) with provable information-theoretic bounds. Focus shifts from heuristic compression to principled distortion-rate optimization, with practical applications to long-context LLM inference and vector database search.

## Open problems

- Optimal quantization of structured, sparse vectors
- Joint optimization of quantization across multiple model components (weights, activations, KV)
- Adaptive, learned quantization strategies per-model and per-task
- Quantization of attention patterns and token selection
- Extension to non-Euclidean metrics and structured similarity measures
- Low-bitwidth quantization (<2 bits) without significant accuracy loss

## Contradictions and debates

- **Online vs. offline quantization**: Trade-offs between simplicity (online, data-oblivious) vs. optimization potential (offline, data-aware)
- **MSE vs. task-specific metrics**: Whether MSE-optimal quantization translates to downstream task performance
- **Uniform vs. non-uniform quantization**: Benefits of per-layer or per-channel adaptive bit allocation

## Recommended reading order

1. **[[papers/2025-turboquant-online-vector-quantization|TurboQuant (2025)]]** — Start here for the theoretical foundations and practical applications of vector quantization with near-optimal bounds

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
