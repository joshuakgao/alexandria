---
topic: Synthetic Datasets
slug: synthetic-datasets
---

# Synthetic Datasets

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "synthetic-datasets")
SORT year DESC
```

## Overview

*Last updated: 2026-04-07 | Sources: 2 papers*

## Current thesis

Synthetic datasets for scene understanding have evolved from game-engine-based multi-task benchmarks (2017) to professionally-curated photorealistic datasets with public assets and disentangled rendering (2021). Modern approaches combine high-quality photorealistic rendering, comprehensive per-pixel annotations (geometry, semantics, materials, lighting), and diverse environmental conditions to achieve strong sim-to-real transfer. The field demonstrates that synthetic data generation is economically competitive with manual annotation and enables comprehensive multi-task learning that would be infeasible with real-world data collection.

## Key trends

**2017–2021 evolution:**

1. **Game engines as data sources** (2017): [[2017-playing-for-benchmarks-gta-dataset|Playing for Benchmarks]] demonstrates multi-task benchmark creation from GTA V through novel bytecode-level extraction methodology
   - Multi-task video benchmarking (low-level and high-level tasks simultaneously)
   - Diverse environmental conditions (day, night, rain, snow)
   - Temporal consistency across video sequences

2. **Professional asset-based datasets** (2021): [[2021-hypersim-photorealistic-synthetic-dataset-indoor-scene|Hypersim]] advances toward curated photorealistic datasets with public assets
   - Photorealistic rendering via professional 3D models
   - Image factorization enabling inverse rendering (diffuse/specular decomposition)
   - Cost-efficiency (generation ≈ 0.5× cost of large NLP model training)
   - Strong sim-to-real transfer on real benchmarks

3. **Future directions**: Combining multi-task scope of game engines with photorealism and public assets of curated datasets

## Open problems

- How can synthetic data generation be further accelerated to enable real-time dataset creation?
- What is the minimal level of photorealism needed for strong sim-to-real transfer across different tasks?
- How can synthetic datasets handle dynamic scenes, temporal variation, and complex material properties?
- Can synthetic datasets be generated in a task-agnostic way that generalizes across diverse applications?
- How do synthetic datasets perform on completely novel environments or architectural styles not in training data?

## Contradictions and debates

*No contradictions yet—field is relatively new.*

## Recommended reading order

1. Start with [[papers/2017-playing-for-benchmarks-gta-dataset|Playing for Benchmarks]] to understand game-engine-based data extraction and multi-task benchmarking
2. Then read [[papers/2021-hypersim-photorealistic-synthetic-dataset-indoor-scene|Hypersim]] to see evolution toward curated photorealistic datasets with public assets
3. Explore concept pages ([[concepts/synthetic-datasets|Synthetic Datasets]], [[concepts/sim-to-real-transfer|Sim-to-Real Transfer]]) for deeper context

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
