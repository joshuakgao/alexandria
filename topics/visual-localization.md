---
topic: Visual Localization
slug: visual-localization
---

# Visual Localization

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "visual-localization")
SORT year DESC
```

## Overview

*Last updated: 2026-04-07 | Sources: 2 papers*

## Current thesis

Visual localization research has evolved from hierarchical coarse-to-fine matching within a single task (2019) to unified multi-task image retrieval models (2025). Modern approaches recognize that retrieval is foundational across Visual Place Recognition, Landmark Retrieval, Visual Localization, 3D Reconstruction, and SLAM—each with different definitions of "same place." Unified models trained on diverse datasets with task-aware sampling strategies outperform specialized approaches, suggesting that shared geometric and appearance priors generalize well across tasks.

## Key trends

**2019–2025 evolution:**

1. **Hierarchical matching** (2019): Two-stage coarse-to-fine using global retrieval + local matching, enabling efficient and robust localization
   - Learned sparse features outperform classical methods
   - Joint prediction in monolithic networks improves efficiency
   - Mobile deployment via multitask distillation

2. **Unified multi-task retrieval** (2025): Single model achieving SOTA across VPR, LR, VL, 3D Reconstruction, SLAM
   - Recognition that different tasks have different definitions of "same place"
   - Task-aware sampling strategies (EigenPlaces, CliqueMining, visual overlap checks)
   - Large-scale datasets (SF-XL with 41M images) provide diverse training data
   - Shared geometric priors generalize across task boundaries

## Open problems

- How to handle extreme appearance changes (e.g., after years of environmental change, urban redevelopment)?
- Can temporal/sequential information improve localization consistency in video streams?
- What is the minimal size of a reference 3D model while maintaining localization accuracy?
- How do learned features perform in highly ambiguous environments (e.g., repetitive architecture, urban canyons)?
- Can localization be improved through online refinement/adaptation to new environments?

## Contradictions and debates

*No contradictions yet—early topic development.*

## Recommended reading order

1. Start with [[papers/2019-hierarchical-localization-at-large-scale|HF-Net]] to understand foundational hierarchical matching and efficient feature learning
2. Then read [[papers/2025-megaloc-one-retrieval-to-place-them-all|MegaLoc]] to see how the field evolved toward unified multi-task retrieval
3. Explore concept pages ([[concepts/visual-localization|Visual Localization]], [[concepts/learned-features|Learned Features]], [[concepts/image-retrieval|Image Retrieval]]) for deeper context

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
