---
title: "Env-QA: A Video Question Answering Benchmark for Comprehensive Understanding of Dynamic Environments"
authors: [Daniel Gao, Howard Wang, Sai Bai, Hao Chen]
year: 2021
venue: "ICCV"
tags: [embodied-memory, video-understanding, benchmarking]
url: ""
pdf: "[[2021-env-qa.pdf]]"
date_ingested: 2026-06-18
---

# Env-QA: A Video Question Answering Benchmark for Comprehensive Understanding of Dynamic Environments

![[2021-env-qa-thumbnail.png]]

## Research gap

Env-QA is a video question answering benchmark specifically designed to evaluate AI systems' understanding of dynamic environments — the ability to perceive an environment, track object state changes caused by interactions, and reason temporally about events. Unlike existing video QA datasets that focus on human-centric actions, movie plots, or social situations, Env-QA uses egocentric videos of agents exploring and interacting with indoor environments in AI2-THOR, where each video contains a series of manipulation events (moving, opening, turning on, slicing, etc.) that progressively change the environment state.

## Contributions

- Env-QA: the first video QA benchmark focused on dynamic environment understanding from egocentric interaction videos (23.3K videos, 85.1K QA pairs)
- Five question types covering environment composition, object state tracking, event recognition, temporal reasoning, and counting
- Semi-automatic dataset construction with controlled distribution using AI2-THOR simulator, balancing sample diversity with annotation quality
- TSEA model with event-level video representation (content-based temporal segmentation) and multi-step event attention for temporal reasoning
- Role-value evaluation format enabling fine-grained accuracy measurement (action, object, preposition, adjective, yes/no, number)

## Method

**Dataset Construction:** Videos are generated in AI2-THOR across 120 indoor environments (kitchens, living rooms, bedrooms, bathrooms) with 115 object types and 15 action types. Five video types (exploring, random, object-centric, action-centric, comprehensive task) target different abilities. A template-based question generator with answer distribution balancing produces candidate QA pairs, which annotators then rephrase and verify.

**TSEA Model:**
1. **Event-level feature extraction:** Faster R-CNN extracts region features; temporal CNN encodes short-term temporal info; a focus attention mechanism weights objects by proximity to image center (important in egocentric view). A heuristic algorithm segments the video into variable-length clips based on content changes (when the set of focused objects changes significantly).
2. **Multi-step temporal attention:** GRU encodes the question with self-attention to extract temporal cues (e.g., "before", "after"). Multi-step attention iteratively attends over event-level features guided by different parts of the question.
3. **Role-value answer prediction:** Separate classifiers predict each answer role (Action, Object1, Object2, Prep., Adjective, Yes/No, Number), enabling partial credit and fine-grained error analysis.

## Datasets & evaluation

- TSEA achieves 47.1% overall accuracy (vs. 38.1% CNN-LSTM, 43.4% STAGE)
- Ablation shows each component contributes: multi-step attention (+1.6%), event features (+2.1%), focus attention (+1.6%), object name features (+1.2%)
- Generalization: similar performance on seen (46.9%) and unseen (47.2%) environments
- Key challenges: Event queries (34.5%) and Number queries (38.0%) are hardest, indicating weak multi-event temporal reasoning and counting
- State queries improve most from TSEA (50.5% vs. 42.3% CNN-LSTM), showing event-level representation helps state tracking
- Question-only baseline achieves 32.5%, indicating meaningful visual grounding is required

## Limitations

- Videos are generated in AI2-THOR simulator; transfer to real-world egocentric videos (with noise, occlusion, continuous motion) is untested
- The heuristic temporal segmentation algorithm may miss subtle state changes; learned segmentation could improve
- 47% accuracy is far below human performance; the paper identifies temporal reasoning and counting as primary bottlenecks
- The answer vocabulary is fixed; open-ended generation would better capture the richness of environment descriptions
- Multi-room and longer-horizon scenarios (minutes to hours) with many more events are not explored
- Integration with 3D scene representations (scene graphs, spatial maps) suggested but not implemented

## Key takeaways

The dataset contains 23.3K egocentric videos and 85.1K question-answer pairs. Five question types evaluate different facets of environment understanding: (1) Attribute queries (static environment properties like object color), (2) State queries (object positions/states after interactions), (3) Event queries (what happened before/after an event), (4) Order queries (temporal ordering of events), and (5) Number queries (counting events or objects). A semi-automatic construction method controls sample distribution while annotators ensure naturalness.

The paper also proposes TSEA (Temporal Segmentation and Event Attention), a video QA model that introduces event-level video representation — segmenting videos into variable-length clips based on content rather than fixed intervals — and multi-step temporal attention to locate key events for answering questions. TSEA achieves 47.1% overall accuracy, revealing that long-term state tracking, multi-event temporal reasoning, and event counting remain formidable challenges.

Env-QA fills a gap between existing video QA datasets (MovieQA, TGIF-QA, TVQA — focused on human actions/plots) and embodied AI tasks (EmbodiedQA, InteractiveQA — focused on action planning in static environments). Unlike EmbodiedQA which evaluates vision implicitly through action quality, Env-QA directly evaluates visual understanding via QA. Compared to CLEVRER (synthetic object collisions with causal reasoning), Env-QA focuses on realistic indoor interactions with richer event types. The event-level video representation contrasts with grid-level temporal sampling used in prior video QA methods. This work anticipates later embodied memory systems that must track environment state changes over long horizons.
