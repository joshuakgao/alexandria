---
topic: Floorplan Reconstruction
slug: floorplan-reconstruction
---

# Floorplan Reconstruction

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "floorplan-reconstruction")
SORT year DESC
```

## Overview

# Floorplan Reconstruction — Overview

*Last updated: 2026-06-01 | Sources: 2 papers*

## Current thesis

Floorplan reconstruction has converged on Transformer-based polygon prediction, with the field now focused on improving robustness through better representations and initialization. RoomFormer (2023) established the paradigm of direct polygon set prediction from density maps. PolyRoom (2024) advances this by replacing sparse corner-only sequences with dense uniform sampling, and random learned queries with instance-segmentation-initialized queries — framing reconstruction as progressive refinement of initial room shapes rather than generation from scratch. The room-aware self-attention decomposition (intra-room + inter-room) further improves both efficiency and accuracy. Both papers confirm that rooms are naturally represented as polygon sequences, but PolyRoom shows that dense supervision and meaningful initialization are critical for handling complex layouts.

## Key trends

**2023:**
- **End-to-end structured prediction replaces multi-stage pipelines.** RoomFormer eliminates the sequential corner→edge→room or room→polygon pipelines of prior work (Floor-SP, HEAT, MonteFloor). Two-level queries enable simultaneous prediction of all rooms with cross-room reasoning (shared walls, spatial consistency).
- **DETR-style set prediction extended to variable-length sequences.** Unlike DETR (fixed-size bounding boxes) or LETR (line segments), RoomFormer generates variable-length ordered sequences, a fundamental extension of the set prediction paradigm to structured geometric outputs.
- **Density map as compact representation.** Projecting 3D point clouds to 2D density maps provides an efficient intermediate that highlights structural elements while remaining computationally tractable. This early transition to 2D is standard across the field.

**2024:**
- **Dense uniform sampling over sparse corners.** PolyRoom replaces RoomFormer's variable-length corner-only sequences with fixed-length uniformly-sampled vertex sequences, enabling dense supervision along all walls. This prevents catastrophic failures from missing/biased corners and enables angle supervision.
- **Instance-segmentation-initialized queries.** Rather than learning queries from scratch, PolyRoom initializes from predicted room masks — providing geometrically meaningful starting shapes that the Transformer decoder refines. This substantially improves convergence and final accuracy.
- **Room-aware attention decomposition.** Splitting self-attention into intra-room (vertex-to-vertex within a room) and inter-room (room-to-room) components reduces complexity from O((MN)²) to O(M²+N²) while capturing the natural structure of floorplans.

## Open problems

- **Scalability to complex layouts**: fixed maximum room/corner counts limit handling of very large or complex buildings
- **Multi-story and split-level buildings**: density map projection discards height information
- **Non-Manhattan geometries**: curved walls, irregular room shapes, and organic architecture remain challenging
- **Real-world robustness**: limited evaluation on large-scale noisy real scans; point cloud quality directly impacts density map fidelity
- **Wall modeling**: current approaches treat walls as zero-thickness edges between room polygons; physical wall thickness and materials not modeled
- **Integration with 3D understanding**: floorplans are 2D abstractions; connecting them back to 3D scene understanding and semantic graphs is underexplored

## Contradictions and debates

- **Top-down vs. bottom-up vs. direct prediction**: three paradigms coexist, with RoomFormer/PolyRoom arguing for direct prediction. Whether this holds for very large or highly irregular scenes where intermediate representations might provide useful constraints remains open.
- **Sparse vs. dense polygon representation**: RoomFormer uses corner-only (sparse, elegant, variable-length); PolyRoom uses uniform sampling (dense, fixed-length, more robust). Dense supervision wins on accuracy, but sparse representation is more compact and naturally captures the structure of floorplans.

## Recommended reading order

1. [[papers/2023-roomformer|RoomFormer (2023)]] — foundational single-stage approach; establishes the structured prediction formulation with two-level queries; comprehensive comparison to both top-down and bottom-up baselines
2. [[papers/2024-polyroom|PolyRoom (2024)]] — addresses RoomFormer's limitations with dense uniform sampling, room-aware query initialization, and decomposed self-attention; demonstrates the importance of representation and initialization choices

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
