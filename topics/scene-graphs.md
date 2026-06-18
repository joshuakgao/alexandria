---
topic: Scene Graphs
slug: scene-graphs
---

# Scene Graphs

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "scene-graphs")
SORT year DESC
```

## Overview

*Last updated: 2026-04-24 | Sources: 1 paper*

## Current thesis

3D scene graphs provide a unified, hierarchical representation for encoding spatial and semantic information in environments. They bridge the gap between raw geometry (meshes, point clouds) and high-level semantic understanding (objects, rooms, buildings), making them valuable for robotics and embodied AI applications. The frontier is shifting toward real-time construction—historically, scene graph generation required offline batch processing, but recent work (Hydra) demonstrates feasibility of online incremental construction with accuracy comparable to offline methods.

## Key trends

**2022+: Real-time shift.** Early scene graph work focused on offline parsing of pre-reconstructed environments or complete sequences. Hydra demonstrates that hierarchical scene graphs can be constructed incrementally in real-time, opening up applications in online robotics and embodied agents. The key insight: using local, windowed representations (ESDF) and graph-based place extraction (GVD) avoids the memory and computational scaling problems of batch processing.

## Open problems

- **Semantic annotation**: Current systems detect geometric structures (rooms, places) but don't label them semantically ("kitchen," "hallway," "conference room")
- **Real-time semantic understanding**: How to integrate semantic understanding (object recognition, scene classification) into the real-time pipeline without sacrificing latency
- **Scalability to outdoor/large-scale environments**: Work so far focuses on indoor, bounded spaces; outdoor and multi-building scenes remain largely unexplored
- **Dynamic environments**: Scene graphs are built for static or near-static scenes; handling dynamic objects and moving agents needs more work
- **Downstream task integration**: What makes a scene graph representation good for downstream tasks (planning, navigation, instruction following)? Currently underexplored

## Contradictions and debates

(None yet—only one paper in the corpus)

## Recommended reading order

1. **[[papers/2022-hydra]]** — Start here for an overview of the hierarchical scene graph representation and the challenge of real-time construction. Good foundation for understanding the field's direction.

## Trends

*To be updated as more papers are added.*

## Open questions

*To be updated as more papers are added.*
