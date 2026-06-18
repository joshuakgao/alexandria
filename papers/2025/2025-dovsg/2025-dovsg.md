---
title: "Dynamic Open-Vocabulary 3D Scene Graphs for Long-term Language-Guided Mobile Manipulation"
authors: [Zhijie Yan, Shufei Li, Zuoxu Wang, Lixiu Wu, Han Wang, Jun Zhu, Lijiang Chen, Bangyan Liu]
year: 2025
venue: "IEEE RA-L"
tags: [embodied-memory, scene-graphs]
url: ""
pdf: "[[2025-dovsg.pdf]]"
date_ingested: 2026-06-18
---

# Dynamic Open-Vocabulary 3D Scene Graphs for Long-term Language-Guided Mobile Manipulation

![[2025-dovsg-thumbnail.png]]

## Research gap

DovSG is a complete mobile manipulation framework that enables robots to perform long-term tasks in dynamic real-world environments by maintaining and locally updating open-vocabulary 3D scene graphs. The system integrates five modules — perception, memory, task planning, navigation, and manipulation — into an end-to-end pipeline tested on real hardware (xARM6 on Agilex Ranger Mini 3 mobile base).

## Contributions

- Dynamic 3D scene graph with efficient local update mechanism that avoids full scene reconstruction (27x faster than ConceptGraphs)
- Multi-stage relocalization pipeline (ACE → LightGlue → colored ICP) for accurate pose estimation in changed environments
- Voxel-level obsolescence detection via depth/color comparison for surgical memory updates
- Complete mobile manipulation system validated on real hardware across 240 long-term task trials in dynamic environments
- 30% higher long-term task success rate over static-scene baselines

## Method

**Object mapping**: RGB-D frames are processed through Recognize-Anything → Grounding DINO → SAM-2 for open-vocabulary segmentation. CLIP features are extracted from both cropped and masked images (fused via weighted sum as in HOV-SG). Detected objects are projected to 3D, clustered via DBSCAN, and associated with existing map objects using a weighted combination of geometric overlap (nearest-neighbor rate), visual similarity (CLIP RGB), and text similarity (CLIP text).

**Scene graph**: Objects are voxelized and spatial relationships (on, belong, inside) are computed from 3D positions. Edges are inferred from geometric containment (alpha shapes, Delaunay triangulation) and vertical stacking.

**Dynamic update**: (1) Relocalize robot via ACE + LightGlue + ICP. (2) Project stored voxels into new camera views; delete voxels with depth/color discrepancies beyond thresholds. (3) Process new observations through the same mapping pipeline and fuse with existing objects. (4) Identify affected subgraph (changed objects + their parents/children) and recompute only those edges.

**Task planning**: GPT-4o decomposes natural language tasks into (action_name, object_name) subtask sequences. Navigation uses CLIP-based object retrieval + A* path planning. Manipulation uses AnyGrasp with point cloud cropping and heuristic fallback strategies.

## Datasets & evaluation

| Metric | Minor Adjustment | Appearance | Positional Shift |
|--------|-----------------|------------|-----------------|
| Scene Change Detection (SCDA) | 95.37% | 93.22% | 94.23% |
| Scene Graph Accuracy (SGA) | 88.75% | 84.86% | 83.72% |
| Long-term Task Success (DovSG) | 33/80 | 28/80 | 23/80 |
| Long-term Task Success (Ok-Robot) | 12/80 | 0/80 | 0/80 |

DovSG uses 0.15 GB memory vs. Ok-Robot's 2 GB (13x reduction) and updates in 1 min vs. ConceptGraphs' 27 min. Subtask success rates: navigation 94%, place 87%, pick-up 81%.

## Limitations

- Long-term task success rate (35%) remains low in absolute terms — failure cascades across subtask chains remain a major challenge
- Visual relocalization depends on distinctive visual cues; performance degrades in textureless or repetitive environments
- Limited relation vocabulary (on, belong, inside) — same constraint as Embodied VideoAgent
- Object segmentation errors can produce duplicate nodes in the scene graph, affecting SGA accuracy
- CLIP feature residuals can mislead navigation to historical object positions after positional shifts
- No evaluation on environments with continuous/gradual changes — only discrete manual modifications tested

## Key takeaways

The memory module operates at two levels: a **low-level semantic memory** storing voxelized 3D object point clouds with CLIP visual and text features, and a **high-level scene graph** capturing spatial relationships (on, belong, inside) between objects. The key innovation is **local scene graph updating**: when the robot collects new RGB-D observations during task execution, it performs relocalization (ACE + LightGlue + ICP refinement), removes obsolete voxels by comparing depth/color against new observations, fuses new detections into the existing object map, and locally recomputes only the affected subgraph edges. This avoids full scene reconstruction and achieves update times 27x faster than ConceptGraphs.

The perception pipeline combines Recognize-Anything (tagging) → Grounding DINO (detection) → SAM-2 (segmentation) → CLIP (features) for open-vocabulary 3D object mapping with multi-view association and fusion. Task planning uses GPT-4o to decompose natural language instructions into subtasks (GoTo, PickUp, Place), which are executed via A* navigation and AnyGrasp-based manipulation with heuristic grasp strategies.

Evaluated across 4 real-world rooms with 240 long-term task trials under three levels of environmental modification (minor adjustment, appearance changes, positional shifts), DovSG achieves 35% long-term task success rate vs. Ok-Robot's 5%, a 30% improvement attributed to dynamic scene adaptation. Scene change detection accuracy reaches 94%+ across all modification types.

Directly builds on **HOV-SG**'s hierarchical scene graph and CLIP feature fusion methodology, extending it with dynamic local updates. Addresses the static-scene limitation that HOV-SG, ConceptGraphs, and Ok-Robot share. The local subgraph update strategy — identifying affected objects and their graph neighbors, then recomputing only those edges — is more surgical than **Embodied VideoAgent**'s VLM-based state updates, operating at the voxel level rather than field-level object entries. Complements **MoMa-LLM**, which also uses dynamic scene graphs for LLM-grounded interactive search but focuses on semantic reasoning rather than physical manipulation. The relocalization pipeline (ACE + LightGlue + ICP) addresses the pose estimation challenge that **Embodied VideoAgent** handles via COLMAP/DUSt3R. Unlike most embodied memory systems in this topic that focus on QA/navigation, DovSG targets end-to-end mobile manipulation with real hardware validation.
