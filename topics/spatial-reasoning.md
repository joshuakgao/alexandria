---
topic: Spatial Reasoning
slug: spatial-reasoning
---

# Spatial Reasoning

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "spatial-reasoning")
SORT year DESC
```

## Overview

*Last updated: 2026-06-16 | Sources: 1 paper*

## Current thesis

Multimodal LLMs exhibit emerging but substantially subhuman visual-spatial intelligence. The primary bottleneck is spatial reasoning itself — not perception, language, or temporal processing — with egocentric-allocentric transformation and relational reasoning accounting for ~71% of errors. Models build local spatial world models but fail to form globally consistent representations.

## Key trends

- **Benchmarking spatial intelligence:** VSI-Bench (CVPR 2025) establishes a rigorous video-based evaluation framework with 8 task types, moving beyond image-based or text-only spatial assessments.
- **Cognitive map probing:** Prompting models to externalize spatial representations reveals that MLLMs form accurate local models (64% accuracy for adjacent objects) but accuracy degrades with distance, suggesting a fragmented rather than unified spatial understanding.
- **Failure of linguistic prompting:** Chain-of-thought, self-consistency, and tree-of-thoughts all hurt spatial performance, indicating that spatial reasoning requires fundamentally different approaches than linguistic reasoning enhancement.
- **Measurement estimation as relative strength:** The human-model gap is narrowest on quantitative estimation tasks (distance, size), suggesting models may have reasonable scale priors from training data.

## Open problems

- How to train models that build globally consistent spatial representations from sequential visual input
- Whether spatial-specific architectures or training objectives can close the 34-point human-model gap
- Extending spatial reasoning evaluation to outdoor, large-scale, and multi-room environments
- Integration of explicit spatial representations (3D maps, point clouds) with MLLM reasoning
- Whether spatial reasoning can be improved through spatial pretext tasks like cognitive map prediction

## Contradictions and debates

- Linguistic prompting techniques (CoT, ToT) are established as beneficial for reasoning tasks generally, but VSI-Bench shows they are actively harmful for spatial reasoning — the boundary between these regimes is not well understood.

## Recommended reading order

1. **[[papers/2025-vsi-bench|Thinking in Space (VSI-Bench)]]** — foundational benchmark and analysis establishing that spatial reasoning is the primary bottleneck for MLLMs

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
