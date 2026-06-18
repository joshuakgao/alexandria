---
topic: Dynamic Scene Graph Generation
slug: dynamic-scene-graph-generation
---

# Dynamic Scene Graph Generation

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "dynamic-scene-graph-generation")
SORT year DESC
```

## Overview

*Last updated: 2026-04-12 | Sources: 2 papers*

## Current thesis

Dynamic scene graph generation extends static scene graphs to videos by incorporating temporal reasoning. The field has evolved from two-stage spatial-temporal transformers (STTran) to one-stage end-to-end approaches (OED). Current methods prioritize joint optimization of all components—detection, tracking, and relation classification—through transformer-based architectures that model both spatial relationships within frames and temporal dependencies across frames for structured understanding of evolving object interactions.

## Key trends

**2021**: Introduction of spatial-temporal transformers (STTran) with decoupled spatial encoder and temporal decoder. Demonstrates that structured temporal reasoning outperforms applying static methods independently to each frame.

**2024**: Shift toward one-stage end-to-end paradigms (OED) eliminating explicit detection and tracking stages. Progressively Refined Modules enable implicit temporal modeling without external trackers, reducing computational overhead and error propagation from sequential stages.

## Open problems

- Scaling to videos with many objects and complex activity hierarchies
- Handling object tracking and association across frames
- Temporal reasoning over longer video sequences
- Generalization across diverse video domains and interaction types
- Efficient inference for real-time applications

## Contradictions and debates

- **Activity decomposition**: Whether fine-grained temporal scene graphs provide benefits over coarse activity labels for downstream tasks remains to be thoroughly investigated
- **Supervision requirements**: Trade-offs between fully-supervised training on annotated scene graphs vs. weakly-supervised or self-supervised approaches

## Recommended reading order

1. **[[papers/2021-spatial-temporal-transformer-dynamic-scene-graph|STTran (2021)]]** — Start here for the foundational spatial-temporal transformer approach to dynamic scene graphs
2. **[[papers/2024-oed-one-stage-end-to-end-dynamic-scene-graph|OED (2024)]]** — See the evolution toward one-stage end-to-end methods and Progressively Refined Modules

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
