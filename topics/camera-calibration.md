---
topic: Camera Calibration
slug: camera-calibration
---

# Camera Calibration

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "camera-calibration")
SORT year DESC
```

## Overview

*Last updated: 2026-06-01 | Sources: 1 paper*

## Current thesis

Single-image camera calibration is shifting from global parameter estimation (predicting a single roll/pitch/FoV vector) to dense per-pixel geometric representations. Perspective Fields (2023) demonstrates that predicting per-pixel Up-vectors and Latitude values — a local, non-parametric representation — is more robust than global methods, especially on cropped or edited images where the centered-principal-point assumption fails. This mirrors broader trends in visual geometry where per-pixel representations (depth maps, normal maps) have replaced global descriptors.

## Key trends

- **Per-pixel over global parameters**: Perspective Fields shows that dense prediction of local perspective properties halves calibration error vs. global methods and is invariant to cropping — a critical property for images in the wild
- **Camera-model agnostic representations**: The Up-vector + Latitude formulation works across pinhole, fisheye, and equirectangular projections without model-specific assumptions
- **Training from panoramas**: 360° panoramas provide virtually unlimited ground truth for arbitrary camera configurations, enabling training without calibration targets or multi-view setups
- **Perspective-aware compositing**: PFD metric connects calibration to image editing applications, showing that calibration quality directly impacts perceptual realism

## Open problems

- Extending per-pixel calibration to video with temporal consistency
- Joint prediction of perspective and depth for unified geometric understanding
- Handling extreme lens distortions and non-standard projections beyond what panorama crops can simulate
- Integration with downstream 3D reconstruction and embodied perception systems that currently assume known calibration

## Contradictions and debates

- **Local vs. global**: Per-pixel predictions are more robust but require a second stage (ParamNet) to recover global parameters when needed. Whether end-to-end global prediction can match per-pixel robustness with better architectures remains open.

## Recommended reading order

1. **Perspective Fields** (Jin et al., 2023) — Foundational paper establishing per-pixel camera representation; covers both the representation and its applications to calibration and compositing

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
