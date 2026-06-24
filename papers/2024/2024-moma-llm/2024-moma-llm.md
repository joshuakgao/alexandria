---
title: "Language-Grounded Dynamic Scene Graphs for Interactive Object Search with Mobile Manipulation"
authors: [Daniel Honerkamp, Martin Büchner, Fabien Despinoy, Tim Welschehold, Abhinav Valada]
year: 2024
venue: "IEEE RA-L"
tags: [embodied-ai, scene-graphs, agentic-mllms]
url: ""
pdf: "[[2024-moma-llm.pdf]]"
date_ingested: 2026-06-18
---

# Language-Grounded Dynamic Scene Graphs for Interactive Object Search with Mobile Manipulation

![[2024-moma-llm-thumbnail.png]]

## Research gap

MoMa-LLM grounds large language models in dynamically built 3D scene graphs for interactive object search with mobile manipulation in large, unexplored indoor environments. The key insight is that LLMs can leverage their accumulated knowledge about human-world spatial priors (e.g., stoves are in kitchens) for efficient search — but only if the scene is encoded in a structured, compact textual representation rather than raw JSON or simple object lists.

## Contributions

- Dynamic hierarchical scene graph with open-vocabulary room classification via LLM, Voronoi-based navigation, and door-based room segmentation
- Structured compact knowledge extraction that encodes scene structure, partial observability (frontiers, receptacle states), and re-aligned action history for LLM grounding
- Semantic interactive search task extending prior work to realistic room-object distributions with manipulation (doors, cabinets, drawers)
- Novel AUC-E evaluation metric using full efficiency curves rather than arbitrary time budgets
- Zero-shot, open-vocabulary system demonstrated in both simulation and real-world mobile manipulation

## Method

**Scene Graph Construction**: RGB-D → 3D voxel grid → BEV occupancy map. Voronoi graph extracted from ESDF-inflated BEV. Rooms segmented by removing Voronoi edges/nodes near doors (mixture of Gaussians over door positions). Objects assigned to rooms via shortest path on Voronoi graph (Eq. 2) to prevent cross-wall assignment. Room classification via LLM prompted with contained object categories.

**Structured Knowledge Representation**: Three components: (1) Scene structure — rooms + objects in structured list with binned distances; (2) Partial observability — frontiers classified as within-room (occluded space) or leading-out (new areas), receptacle states (open/closed); (3) Dynamic history — last h actions re-aligned to current room labels as scene graph evolves (e.g., "living room" → "kitchen" after fridge discovered).

**High-Level Policy**: LLM (GPT-4) receives structured prompt with chain-of-thought (analysis + reasoning + command). Object-centric action space: navigate(room, object), go_to_and_open(room, object), explore(room), close(room, object), done(). Re-planning on subpolicy failure with minimal success/failure feedback.

**Low-Level Policies**: A* navigation on inflated BEV map via Voronoi nodes, frontier-based exploration, manipulation primitives.

## Datasets & evaluation

Interactive search in iGibson (15 apartments, 84 target classes): 97.7% SR, 63.6 SPL, 87.2 AUC-E. Outperforms ESC-Interactive (95.4/62.7/84.5), HIMOS (93.7/48.5/77.4), and unstructured LLM (86.3/59.4/77.6). Ablations show structured encoding is critical: without frontiers (-18.3% SR), without history (-2.8% SR). MoMa-LLM with Hydra room segmentation achieves 92.0% SR (vs. 97.7% with door-based segmentation), confirming the benefit of door-aware room separation. Only 0.19 infeasible actions per episode on average (vs. 0.41 for unstructured LLM).

## Limitations

- Assumes access to ground truth perception (semantic masks, depth, localization, handle detection) — real deployment would require robust perception pipeline
- Room classification via LLM is heuristic and may fail on unusual room types or mixed-use spaces
- GPT-4 dependency for high-level planning introduces latency and cost; smaller LLMs may struggle with the structured prompting format
- The door-based room segmentation relies on door detection quality; missed doors produce incorrect room boundaries
- Interactive search is limited to opening/closing — more complex manipulation tasks (e.g., moving objects, stacking) are not addressed
- Evaluation is on iGibson with enriched object placements; transfer to more diverse environments is untested

## Key takeaways

The system constructs a hierarchical scene graph from posed RGB-D observations: a semantic 3D map yields BEV occupancy maps, from which a navigational Voronoi graph is extracted; rooms are segmented by removing Voronoi edges near doors, classified via LLM prompting with contained objects, and objects are assigned to rooms via shortest-path distance on the Voronoi graph. This scene graph is dynamically updated as the agent explores. The structured knowledge representation encodes scene structure (rooms + objects), partial observability (frontiers as "unexplored area" within or leading out of rooms, closed receptacles), and action history (re-aligned to current room labels as the graph evolves).

An LLM (GPT-4) produces high-level actions from an object-centric action space (navigate, go_to_and_open, close, explore, done) that are executed by low-level subpolicies. The system is zero-shot, open-vocabulary, and handles interactive tasks requiring manipulation (opening doors, searching inside cabinets/drawers).

MoMa-LLM achieves 97.7% SR and 87.2 AUC-E on the semantic interactive search task in iGibson, outperforming ESC-Interactive (95.4% SR, 84.5 AUC-E), HIMOS (93.7% SR), and an unstructured LLM baseline (86.3% SR). Real-world deployment on a mobile manipulator is demonstrated.

MoMa-LLM builds on the same group's HOV-SG work (same authors: Büchner, Valada), sharing the Voronoi-based navigation and hierarchical scene graph design. Where HOV-SG focuses on open-vocabulary features at each hierarchy level, MoMa-LLM focuses on structured LLM grounding for interactive search — converting the scene graph into compact text that enables efficient LLM planning.

Compared to SayPlan (which operates on pre-built scene graphs), MoMa-LLM builds scene graphs dynamically during exploration. Compared to SayNav (which restricts LLM to room subgraphs), MoMa-LLM provides the full scene structure with partial observability encoding. The structured knowledge representation anticipates MemoryEQA's module-specific memory retrieval — both argue that how you present information to the LLM matters as much as what information you store.

Within embodied memory, MoMa-LLM demonstrates that scene graphs serve as effective memory structures for LLM-based planning, bridging the gap between spatial perception and language-based reasoning. The dynamic re-alignment of action history as the scene graph evolves is a practical solution to the memory consistency problem that other systems handle through deduplication (EmbodiedLGR) or viewpoint-contrastive updates (MemoryEQA).
