---
title: "RoboEXP: Action-Conditioned Scene Graph via Interactive Exploration for Robotic Manipulation"
authors: [Hanxiao Jiang, Binghao Huang, Ruihai Wu, Zhuoran Li, Shubham Garg, Hooshang Nayyeri, Shenlong Wang, Yunzhu Li]
year: 2024
venue: "CoRL 2024"
tags: [embodied-ai, scene-graphs]
url: ""
pdf: "[[2024-roboexp.pdf]]"
date_ingested: 2026-06-18
---

# RoboEXP: Action-Conditioned Scene Graph via Interactive Exploration for Robotic Manipulation

![[2024-roboexp-thumbnail.png]]

## Research gap

RoboEXP introduces the task of interactive scene exploration, where robots autonomously explore environments by physically interacting with objects (opening drawers, doors, lifting covers) to discover hidden items and construct an action-conditioned scene graph (ACSG). Unlike conventional scene graphs that encode only static spatial relations, ACSGs explicitly model action-conditioned relationships — capturing that opening a fridge reveals an apple inside, or that picking up an obstructing condiment is a precondition for opening a cabinet door.

## Contributions

- Action-conditioned 3D scene graph (ACSG): a novel scene representation that explicitly models interactive relationships between objects and actions, including preconditions and discovery relations
- Interactive scene exploration task formulation as ACSG construction via POMDP-inspired active perception with a graph-guided exploration objective
- RoboEXP system combining foundation models (GPT-4V, GroundingDINO, SAM-HQ, CLIP) with explicit memory for zero-shot interactive exploration on a real robot
- Demonstration that constructed ACSGs enable efficient downstream manipulation (table setting, object retrieval) by following topological paths through the graph

## Method

**ACSG Structure:** DAG with object nodes (semantics si, geometry pi) and action nodes (action type ak, low-level primitives Tk). Four edge types encode spatial relations, affordances, discovery relations, and action preconditions.

**Exploration Loop:** At each step, the agent selects an unexplored node most likely to reveal new nodes (based on LMM commonsense), executes the corresponding action, observes new objects, and updates the ACSG. Reward: R = R_graph (new nodes) + R_explore (reduced unknowns) + R_time (efficiency penalty).

**Perception:** GroundingDINO for open-vocabulary detection, SAM-HQ for segmentation, CLIP for per-instance features. Multi-view RGBD fusion into 3D.

**Memory:** Low-level: 3D point clouds with instance merging across viewpoints and depth-based staleness for temporal updates (detecting when objects have moved/changed state). High-level: ACSG with node/edge insertion, deletion, modification based on new observations.

**Decision-Making:** GPT-4V serves dual roles — action proposer (selects skill for unexplored objects based on attributes) and action verifier (checks feasibility given ACSG context, identifies obstructions).

**Action Primitives:** Seven categories covering door/drawer open/close, object pick/place, and camera repositioning. Operate on low-level geometric information from ACSG.

## Datasets & evaluation

- **Drawer-only:** 90% success (vs. 20% GPT-4V baseline), 97% object recovery
- **Door-only:** 90% success (vs. 30%), 100% object recovery
- **Drawer-door:** 70% success (vs. 10%), 91% object recovery
- **Recursive (nested objects):** 70% success (vs. 0%), 80% object recovery
- **Occlusion:** 50% success (vs. 0%), 67% object recovery
- **State recovery:** 100% for most tasks (restores scene to initial state after exploration)
- **Error breakdown:** Failures primarily from perception module (detection/segmentation errors), not decision-making or action execution
- **Downstream manipulation:** Successfully uses constructed ACSGs for complex table-setting tasks with articulated objects, nested objects, and deformable objects

## Limitations

- Failures primarily stem from perception (detection/segmentation errors in GroundingDINO/SAM-HQ); improving visual foundation models would directly improve the system
- Limited to tabletop scenarios with a fixed set of seven action primitives; more diverse environments require expanded skill repertoires
- GPT-4V latency and cost make real-time deployment challenging
- Does not handle dynamic environments where objects move independently (only tracks robot-induced state changes)
- Mobile manipulation experiments (Hello Robot Stretch) are limited; scaling to full household exploration with navigation + manipulation is future work
- The ACSG assumes a DAG structure; cyclic dependencies between actions are not supported

## Key takeaways

The ACSG is a directed acyclic graph with two node types (object nodes with semantics/geometry, action nodes with action type/primitives) and four edge types: object→object (spatial, e.g., "belongs to"), object→action (affordance, e.g., "can be picked up"), action→object (reveals, e.g., "opening reveals banana"), and action→action (precondition, e.g., "must move condiment before opening door"). This representation enables downstream manipulation by simply executing all actions along paths from root to target in topological order.

The RoboEXP system has four modules: (1) perception (GroundingDINO + SAM-HQ + CLIP for open-vocabulary detection/segmentation), (2) memory (2D-to-3D instance fusion with depth-based staleness detection for dynamic updates), (3) decision-making (GPT-4V as both action proposer and action verifier, using the ACSG to identify unexplored nodes and check feasibility), and (4) action (seven primitives: open/close door/drawer, pick/place object, move camera). The system explores in a POMDP-inspired loop, maximizing a reward combining node discovery, uncertainty reduction, and time efficiency.

Evaluated on 50 real-world tabletop scenarios across five task types (drawer-only, door-only, drawer-door, recursive, occlusion), RoboEXP achieves 70-90% success rates, vastly outperforming a strong GPT-4V baseline (0-30%) even when the baseline is given manual action execution as an upper bound.

RoboEXP advances beyond static scene graphs (ConceptGraphs, Hydra, HOV-SG) by modeling action effects and interactive discovery — capturing that some objects can only be found through physical interaction. Compared to LLM-based planners (SayPlan, Code-as-Policies) that assume known/observable objects, RoboEXP operates in environments with unknown hidden objects. The explicit ACSG memory contrasts with GraphPad's inference-time updates — RoboEXP builds the graph through physical interaction while GraphPad patches it through perceptual re-analysis. The POMDP-inspired exploration objective with LMM-guided decision-making represents a unique combination of classical active perception principles with foundation model commonsense.
