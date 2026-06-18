---
topic: Gaussian Splatting
slug: gaussian-splatting
---

# Gaussian Splatting

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "gaussian-splatting")
SORT year DESC
```

## Overview

*Last updated: 2026-06-16 | Sources: 1 paper*

## Current thesis

Gaussian splatting has emerged as the dominant paradigm for real-time novel view synthesis, replacing NeRF's volume rendering with efficient differentiable splatting of explicit Gaussian primitives. The extension to dynamic scenes via deformation fields enables real-time 4D rendering while maintaining compact storage, with the HexPlane-based spatial-temporal encoding proving effective for connecting nearby Gaussians and predicting accurate motion.

## Key trends

- **From NeRF to splatting:** The shift from implicit neural radiance fields to explicit Gaussian primitives has yielded 10-100x rendering speedups while maintaining or improving quality.
- **Dynamic scene extension:** Multiple concurrent approaches emerged to extend 3D-GS to 4D (deformation fields, per-frame storage, temporal Gaussian distributions), with deformation field approaches offering the best quality-efficiency tradeoff.
- **Factorized spatiotemporal encoding:** HexPlane and K-Planes style decomposition of 4D voxels into 2D planes provides memory-efficient feature encoding for temporal deformation prediction.
- **Real-time as the standard:** 4D-GS achieves 30-82 FPS depending on resolution and scene complexity, establishing real-time dynamic rendering as achievable rather than aspirational.

## Open problems

- Handling topological changes (objects appearing, disappearing, splitting) in dynamic scenes
- Scaling to very long sequences and large outdoor environments
- Integrating Gaussian splatting with semantic understanding and downstream tasks
- Improving robustness to extremely fast or complex non-rigid motions
- Unifying multi-object scenes where objects move independently

## Contradictions and debates

- Per-frame Gaussian storage vs. deformation fields: Dynamic3DGS argues for explicit per-frame control while 4D-GS favors compact deformation networks. The tradeoff is flexibility vs. efficiency.
- Single canonical space vs. multiple reference frames: whether one canonical set of Gaussians is sufficient for complex multi-object dynamic scenes remains debated.

## Recommended reading order

1. **[[papers/2024-4d-gaussian-splatting|4D Gaussian Splatting]]** — efficient extension of 3D-GS to dynamic scenes with real-time rendering

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
