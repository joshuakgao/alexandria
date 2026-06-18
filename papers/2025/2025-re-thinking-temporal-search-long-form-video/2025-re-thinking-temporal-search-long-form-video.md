---
title: "Re-thinking Temporal Search for Long-Form Video Understanding"
authors: [Jinhui Ye, Zihan Wang, Haosen Sun, Keshigeyan Chandrasegaran, Zane Durante, Cristobal Eyzaguirre, Yonatan Bisk, Juan Carlos Niebles, Ehsan Adeli, Li Fei-Fei]
year: 2025
venue: "CVPR"
tags: [video-understanding, benchmarking]
url: ""
pdf: "[[2025-re-thinking-temporal-search-long-form-video.pdf]]"
date_ingested: 2026-06-18
---

# Re-thinking Temporal Search for Long-Form Video Understanding

![[2025-re-thinking-temporal-search-long-form-video-thumbnail.png]]

## Research gap

This paper addresses temporal search—finding minimal sets of keyframes (1–5 frames) from tens of thousands to answer video questions—as a fundamental bottleneck in long-form video understanding. The work makes two major contributions: (1) **LV-HAYSTACK**, a benchmark of 480 hours of videos with 15,092 human-annotated temporal search instances across egocentric (Ego4D) and allocentric (LongVideoBench) videos, revealing a significant research gap (current SOTA achieves only 2.1% temporal F1); and (2) **T***, a lightweight temporal search framework inspired by visual search techniques (V*), which reframes temporal search as spatial search by transforming frame sequences into a single large image and applying iterative spatial zooming with dynamic sampling. T* achieves 3x computational efficiency improvement and significantly boosts SOTA models: GPT-4o (50.5% → 53.1%) and LLaVA-OneVision-OV-72B (56.5% → 62.4%) with only 32 frames, while using 4x fewer frames than non-search baselines.

## Contributions

- **LV-HAYSTACK dataset**: First large-scale benchmark for temporal search on real-world long-form videos with 480 hours and 15,092 annotated instances covering both egocentric and allocentric scenarios, revealing SOTA gap
- **T* framework**: Novel reformulation of temporal search as spatial search; transforms frame sequences into image grids and applies iterative zooming across temporal and spatial dimensions
- **Three-stage pipeline**: (1) Question Grounding—identify target and cue objects from query; (2) Iterative Temporal Search—spatial search model detects objects and upsamples promising temporal/visual regions; (3) Downstream Task—VLM answers using selected K keyframes
- **Comprehensive evaluation**: Frame-level metrics (temporal and visual similarity), set-level metrics (precision, recall, F1), and efficiency metrics (frame cost, FLOPs, latency)
- **Key insight**: Image-language models outperform video-language models for this task, validating temporal→spatial reformulation

## Method

T* operates in three stages:

1. **Question Grounding**: Uses a VLM to identify visual cues (target and cue objects) from the textual question
2. **Iterative Temporal Search** (core innovation):
   - Transforms frame sequence into initial image grid (e.g., 8 frames sampled uniformly)
   - Spatial search model detects target and cue objects in the grid
   - Dynamic decision making: if target objects found, zoom-in on high-detection cells; if not found, drop 75% unrelated cells and upsample temporal resolution
   - Repeat zooming-in refinement until search budget exhausted
   - Output: temporal search distribution over frames
3. **Downstream Task**: Sample K keyframes from the distribution and feed to VLM for answer generation

The iterative sampling-scoring-reweighting paradigm balances spatial detail (what to sacrifice) and temporal resolution (where to focus), achieving efficient search through multi-step refinement.

## Datasets & evaluation

- **Efficiency**: 3x computational efficiency (FLOPs) vs. frame-by-frame search; uses 4x fewer frames than non-search baselines for equivalent performance
- **LongVideoBench XL (900–3600s videos)**:
  - GPT-4o: 50.5% → 53.1% (+2.6%, with 32 frames)
  - LLaVA-OneVision-OV-72B: 56.5% → 62.4% (+5.9%, with 32 frames)
- **Benchmark insights**: SOTA methods on LV-HAYSTACK achieve only 2.1% temporal F1, indicating significant gap between current capabilities and human-level temporal search

## Limitations

- **Visual search model assumptions**: T* assumes a pre-trained spatial search model; performance degrades when target/cue objects are not visually salient or have high visual variation
- **Temporal discretization**: Fixed image grid resolution may miss fine-grained temporal details; adaptive grid sizing remains unexplored
- **Generalization across video types**: Evaluated primarily on Ego4D and LongVideoBench; scalability to other domains (surveillance, documentaries) unclear
- **Multi-event reasoning**: T* excels at single-object/single-event queries; multi-event temporal reasoning across distant frames remains challenging
- **Ground-truth keyframe dependency**: LV-HAYSTACK evaluation relies on annotated keyframes; multiple valid keyframe sets may exist for same question

## Key takeaways

Extends temporal search literature (previous work relied on cluster- or agent-based frame-by-frame VLM processing). Builds on visual search techniques (V*) for images, adapting coarse-to-fine spatial search to the temporal domain. Complements concurrent temporal search methods (AKEYS, TimeSearch-R) by proposing an orthogonal reformulation: rather than agent-guided or RL-based search, T* leverages spatial search paradigms and image-language model superiority. Provides first comprehensive benchmark and evaluation framework for temporal search, addressing prior focus on task performance over search capability.
