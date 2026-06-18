---
title: "GraphPad: Inference-Time 3D Scene Graph Updates for Embodied Question Answering"
authors: [Muhammad Qasim Ali, Saeejith Nair, Alexander Wong]
year: 2025
tags: [embodied-memory, scene-graphs, agentic-mllms]
url: ""
pdf: "[[2025-graphpad.pdf]]"
date_ingested: 2026-06-18
---

# GraphPad: Inference-Time 3D Scene Graph Updates for Embodied Question Answering

![[2025-graphpad-thumbnail.png]]

## Research gap

GraphPad addresses a fundamental limitation of embodied AI systems: structured 3D memories (scene graphs) are typically built before the downstream task is known, often missing objects and spatial relations that later prove essential. Rather than exhaustive preprocessing or static overprovisioning, GraphPad enables a VLM to dynamically update its scene graph during inference through language-callable API functions.

## Contributions

- Formalization of language-driven online editing of structured 3D memories as a solution to the task-memory mismatch problem in fixed scene graphs
- GraphPad system where a single VLM both identifies knowledge gaps and updates its 3D scene graph representation via language function calls during inference
- Three modifiability APIs (find_objects, analyze_objects, analyze_frame) that enable targeted perceptual refinement without task-specific engineering
- Demonstration that 5 initial frames + targeted API calls outperform 25-frame baselines, showing efficiency of reasoning-guided perception over brute-force frame processing

## Method

**Scene Graph Construction:** For every k-th RGB frame, VLM detector produces bounding boxes and captions → SAM masks → depth backprojection to point clouds → voxel downsampling → DBSCAN noise removal. Track association uses a voting scheme over visual similarity (CLIP ViT-L/14, threshold 0.7), language similarity (BGE, threshold 0.8), and spatial overlap (5cm, threshold 0.4), requiring 2+ votes for match. Spatial relations (on top of, subpart of, contained in, attached to) are discovered by prompting the VLM with visible objects every 3 frames.

**Modifiability APIs:**
- `find_objects(frame_id, query)`: detects previously unseen objects relevant to query, fuses into graph, updates scratch-pad
- `analyze_objects(frame_id, node_list, query)`: examines specific visible nodes' appearances, stores query-relevant notes in scratch-pad
- `analyze_frame(frame_id, query)`: joint discovery + annotation in a single operation (outperforms node-level variants)

**Agentic Reasoning Loop:** VLM receives SSM + question → examines memory → identifies gaps → selects API + frame + query → integrates results → repeats until sufficient information or max calls reached. Must support final answer with dual evidence: visual (from frames) and semantic (from scratch-pad).

## Datasets & evaluation

- **OpenEQA:** 55.3% accuracy (vs. 52.3% image-only Gemini 2.0 Flash, 36.5% static ConceptGraphs, 57.2% 3D-Mem with GPT-4V)
- **Efficiency:** 5 initial frames + avg 1.9 API calls vs. 25 frames for baseline; 95% of questions answered with ≤5 API calls
- **Ablation:** Navigation log provides largest single gain (+9.6pp); static scene graph adds +1.7pp; modifiability APIs add +3.6pp over static graph
- **Frame-level API (50.5%) outperforms node-level APIs (47.1%)** — VLM naturally prefers holistic frame analysis
- **By category:** Strong gains in attribute recognition (+20.3pp), functional reasoning (+5.7pp); weaker in spatial understanding (-4.7pp) and object localization (-3.0pp) where raw visual processing dominates
- **Search depth:** Accuracy generally increases with depth (46.9% at 0 → 51.2% at 20), though shallow search (1-2 calls) can sometimes hurt by introducing misleading partial information

## Limitations

- No verification mechanism for detection quality — VLM misidentifications propagate into the scene graph and affect downstream reasoning
- Each API call requires a full VLM inference pass (~2-3 seconds), limiting real-time applicability
- The three-API design may not be optimal; different tasks might benefit from specialized APIs not explored
- Tested only on static scene QA; manipulation planning, navigation, and dynamic environments are untested
- Scalability to larger scenes (beyond medium-sized homes) with growing graph/prompt sizes is unvalidated
- GraphPad underperforms image-only baselines on spatial understanding and object localization, suggesting structured memory can sometimes hinder raw spatial reasoning

## Key takeaways

The system comprises a Structured Scene Memory (SSM) with four linked components: (1) a mutable scene graph with object nodes, spatial relations, and visual/language embeddings; (2) a graphical scratch-pad mirroring graph nodes with free-form task-specific notes; (3) a frame memory of sparse keyframes expandable on demand; and (4) a navigation log indexing frame-by-frame content (room, field-of-view, motion, visible nodes) for targeted frame retrieval.

At inference time, the reasoning VLM identifies knowledge gaps relevant to the question and invokes three APIs — `find_objects` (detect unseen entities), `analyze_objects` (annotate existing nodes), and `analyze_frame` (joint discovery + annotation) — to patch omissions in real time. The graph evolves from a coarse initial representation into a task-conditioned model containing precisely the information needed.

On OpenEQA, GraphPad achieves 55.3% accuracy (+3.0pp over image-only Gemini 2.0 Flash baseline) while using 5x fewer input frames (5 vs. 25). Most questions are answered with under 2 API calls on average, demonstrating that targeted perception is more efficient than exhaustive preprocessing.

GraphPad directly addresses limitations identified by empirical analyses showing that missing nodes and relations — not reasoning failures — frequently cause EQA errors. It builds on the Hydra/HOV-SG scene graph pipeline for initial construction but makes the graph mutable during inference. Compared to ReMEmbR and Embodied-RAG (frame-level retrieval without structure), GraphPad maintains explicit relational structure while adding update capability. Unlike DynaMem and Kimera (predefined spatial update policies), GraphPad's updates are reasoning-triggered and task-specific. Compared to 3D-Mem (informative view selection, 57.2% with GPT-4V), GraphPad achieves competitive results (55.3% with Gemini 2.0 Flash) through memory editing rather than memory selection. The scratch-pad mechanism relates to chain-of-thought reasoning but is grounded in the scene graph structure.
