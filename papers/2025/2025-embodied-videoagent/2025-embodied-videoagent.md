---
title: "Embodied VideoAgent: Persistent Memory from Egocentric Videos and Embodied Sensors Enables Dynamic Scene Understanding"
authors: [Qianfan Fan, Jialu Ma, Guangzhi Su, Jiming Guo, Siya Wu, Xin Chen, Hai Li]
year: 2025
tags: [embodied-memory]
url: ""
pdf: "[[2025-embodied-videoagent.pdf]]"
date_ingested: 2026-06-18
---

# Embodied VideoAgent: Persistent Memory from Egocentric Videos and Embodied Sensors Enables Dynamic Scene Understanding

![[2025-embodied-videoagent-thumbnail.png]]

## Research gap

Embodied VideoAgent extends the VideoAgent framework for long-form video understanding to dynamic 3D scene comprehension by fusing egocentric video with embodied sensory inputs (depth maps and camera poses). The key innovation is a **persistent object memory** — a structured per-object representation containing unique ID, category, state description, spatial relations, 3D bounding box, and visual features (both object and context). This memory is constructed by combining open-vocabulary object detection (YOLO-World) with 2D-3D lifting via depth and camera pose, then maintained through object re-identification based on visual similarity and 3D proximity.

## Contributions

- Persistent object memory with structured per-object entries (ID, state, relations, 3D bbox, visual features) built from egocentric video + depth/pose sensors
- VLM-based automatic memory update that uses visual prompting to associate detected actions with specific object entries and update their states
- History buffers (action + visible object) that complement persistent memory with temporal context
- Two-agent framework for generating synthetic embodied user-assistant interaction data
- SOTA on three embodied scene understanding benchmarks

## Method

The pipeline operates on egocentric video frames with per-frame depth maps and 6D camera poses. For each frame:
1. **Object detection**: YOLO-World extracts objects and categories
2. **2D-3D lifting**: Depth maps + camera poses produce 3D bounding boxes
3. **Relation extraction**: Spatial relations (on/uphold, in/contain) computed from 3D bbox geometry
4. **Object re-ID**: Visual similarity (CLIP features) + 3D proximity determines whether a detected object matches an existing memory entry; matched entries update via moving average
5. **VLM-based state update**: When actions are detected, visual prompting identifies target objects and programmatically updates STATE and RO fields

Tools query the memory through SQL-like database access (query_db), temporal localization, spatial localization (returning 3D coordinates), and open-ended VQA on specific frames.

## Datasets & evaluation

| Benchmark | Metric | Score | Gain over baseline |
|-----------|--------|-------|--------------------|
| Ego4D-VQ3D | Succ% | 85.37% | +4.9% over EgoLoc |
| OpenEQA (subset) | LLM-Match | 47.0% | +5.8% over VideoAgent |
| EnvQA | Overall | — | +11.7% over best baseline |

On VQ3D, the open-vocabulary detector (YOLO-World) provides higher QwP% (92.07%) than prior methods, and visual similarity-based re-ID significantly outperforms text-only retrieval. The system is robust to noisy pose estimation from COLMAP and DUSt3R.

## Limitations

- Only two spatial relation types (on/uphold, in/contain) are modeled — richer relation vocabularies could improve scene understanding
- VLM-based state updates rely on discrete action detection — continuous or ambiguous state changes may be missed
- Object re-ID via visual similarity + proximity may fail for visually identical objects at similar locations (perceptual aliasing, as highlighted by Chameleon)
- The two-agent data generation framework is demonstrated but not rigorously evaluated for downstream training impact
- Camera pose requirement, while shown robust to noise, still limits deployment on platforms without depth/pose sensing

## Key takeaways

A critical addition is the **VLM-based memory update mechanism** that handles dynamic state changes. When actions are detected (via an action annotator), the system uses visual prompting — rendering 3D bounding boxes of candidate objects onto the current frame — to have a VLM identify which objects are affected, then programmatically updates their state fields (e.g., STATE → "in-hand" after a grasp). Two history buffers (action buffer and visible object buffer) complement the persistent memory by recording temporal context.

The agent is equipped with four tools (query_db, temporal_loc, spatial_loc, vqa) and seven embodied action primitives (chat, search, goto, open, close, pick, place). A two-agent framework enables synthetic embodied interaction data collection, where one LLM proposes tasks while an Embodied VideoAgent explores the scene to fulfill them.

Evaluated on three benchmarks, Embodied VideoAgent achieves SOTA results: +4.9% on Ego4D-VQ3D (85.37% success rate), +5.8% on OpenEQA (47.0% with GPT-4o), and +11.7% on EnvQA. The system demonstrates robustness to noisy camera poses estimated via COLMAP and DUSt3R.

Directly extends **VideoAgent** by adding persistent object memory with embodied sensor fusion and VLM-based state updates. Shares the structured per-object memory philosophy with **R⁴** (4D knowledge database with semantic/spatial/temporal fields), but differs by incorporating explicit state tracking and action-driven updates rather than passive recording. Complements **MoMa-LLM**'s dynamic scene graph approach — both maintain relational object structures, but Embodied VideoAgent operates from egocentric observations rather than pre-built scene graphs. The VLM-based memory update partially addresses the **dynamic environments** open problem identified in the overview: unlike R⁴ which only tracks appearance/disappearance timestamps, Embodied VideoAgent tracks continuous state changes (e.g., "closed" → "open"). Evaluated on **OpenEQA**, the same benchmark used by R⁴, MemoryEQA, and Mind Palace, enabling direct comparison.
