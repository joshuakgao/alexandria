---
topic: Point Cloud Registration
slug: point-cloud-registration
---

# Point Cloud Registration

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "point-cloud-registration")
SORT year DESC
```

## Overview

*Last updated: 2026-04-12 | Sources: 2 papers*

## Current thesis

Learning-based point cloud registration has evolved from end-to-end learnable variants of classical ICP (using attention-based soft matching) to modern geometric transformers with explicit invariance properties. The field is progressing toward RANSAC-free methods that handle challenging low-overlap and large-transformation scenarios through explicit geometric constraints and coarse-to-fine correspondence strategies.

## Key trends

- **From ICP to end-to-end learning (2019)**: [[papers/2019-deep-closest-point|DCP]] reimagined classical ICP as a learnable pipeline, replacing hard nearest-neighbor matching with soft attention-based correspondence and adding differentiable SVD. This foundational approach showed that learned features generalize across object categories.
- **Shift from point-level to patch-level matching (2023)**: [[papers/2023-geotransformer|GeoTransformer]] moved beyond point-wise matching to superpoint (patch) matching with coarse-to-fine propagation, enabling better handling of low-overlap scenarios and reducing correspondence ambiguity.
- **Explicit geometric invariance**: Modern methods encode rotation invariance (pairwise distances) and translation equivariance (angles) directly into feature representations, improving robustness and enabling RANSAC-free transformation estimation.
- **Scalability and efficiency**: Evolution from single-image ModelNet40 evaluations to real-world multimodal benchmarks (3DMatch, 3DLoMatch); RANSAC elimination provides 100× speedup.

## Open problems

- **Extreme low-overlap registration**: Performance degradation below 5% overlap; how to handle cases with minimal geometric context
- **Non-rigid deformation**: Balancing geometric consistency constraints with shape variation in dynamic or deformable objects
- **Symmetric ambiguity**: Handling symmetric point clouds where geometric invariants alone cannot resolve multiple valid solutions
- **Scalability**: Extending coarse-to-fine methods to very large point clouds without quadratic correspondence search

## Contradictions and debates

- **Point-level vs. patch-level matching**: DCP matches individual points using soft attention; GeoTransformer first matches patches (superpoints) then propagates. Which is fundamentally more robust in low-overlap remains an open design question.
- **Hand-crafted vs. learned geometric invariance**: DCP learns features end-to-end without explicit geometric constraints; GeoTransformer encodes pairwise distances and angles directly. GeoTransformer's explicit approach yields higher inlier ratios, suggesting geometric priors are valuable.

## Recommended reading order

For someone new to point cloud registration:
1. [[papers/2019-deep-closest-point|Deep Closest Point (2019)]] — Foundational learning-based approach; understand why ICP fails and how attention-based soft matching improves robustness
2. [[papers/2023-geotransformer|GeoTransformer (2023)]] — Modern extension with explicit geometric invariants and coarse-to-fine strategy; see how geometric priors and patch-level matching further improve performance

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
