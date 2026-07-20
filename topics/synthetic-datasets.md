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

*Last updated: 2026-07-17 | Sources: 3 papers*

## Current thesis

Synthetic datasets for scene understanding have evolved from game-engine-based multi-task benchmarks (2017) to professionally-curated photorealistic datasets with public assets and disentangled rendering (2021). A parallel thread uses existing game environments as open-ended benchmarking platforms — MineDojo (2022) leverages Minecraft's procedurally generated 3D world combined with internet-scale multimodal knowledge (730K+ YouTube videos, Wiki pages, Reddit posts) to define 3,000+ diverse tasks, two orders of magnitude larger than prior Minecraft benchmarks. Modern approaches combine high-quality rendering, comprehensive annotations, and diverse environmental conditions to achieve strong sim-to-real transfer. The field demonstrates that synthetic data generation — whether through photorealistic rendering or game-engine environments — is economically competitive with manual annotation and enables comprehensive multi-task learning that would be infeasible with real-world data collection.

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

3. **Game environments as open-ended benchmark platforms** (2022): MineDojo uses Minecraft as a procedurally generated testbed with programmatic tasks (survival, harvest, combat, tech tree) and creative tasks (building, exploration), evaluated via learned video-language models rather than hand-crafted metrics
   - Internet-scale knowledge bases (YouTube, Wiki, Reddit) as training data for embodied agents
   - Learned reward functions (MineCLIP) replace hand-engineered dense rewards
   - Task generation at scale via YouTube mining and LLM-based brainstorming

4. **Future directions**: Combining multi-task scope of game engines with photorealism and public assets of curated datasets; leveraging internet-scale gameplay data for sim-to-real transfer

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
3. Read [[papers/2022/2022-minedojo/2022-minedojo|MineDojo]] for open-ended game-environment benchmarking with internet-scale knowledge bases and learned reward functions
4. Explore concept pages ([[concepts/synthetic-datasets|Synthetic Datasets]], [[concepts/sim-to-real-transfer|Sim-to-Real Transfer]]) for deeper context

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
