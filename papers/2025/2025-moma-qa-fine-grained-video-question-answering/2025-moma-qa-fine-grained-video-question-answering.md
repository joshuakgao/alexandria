---
title: "Towards Fine-Grained Video Question Answering"
authors: [Wei Dai, Alan Luo, Zane Durante, Debadutta Dash, Arnold Milstein, Kevin Schulman, Ehsan Adeli, Li Fei-Fei]
year: 2025
venue: ""
tags: [video-understanding, scene-graphs, vision-language-models]
url: ""
pdf: "[[2025-moma-qa-fine-grained-video-question-answering.pdf]]"
date_ingested: 2026-06-18
---

# Towards Fine-Grained Video Question Answering

![[2025-moma-qa-fine-grained-video-question-answering-thumbnail.png]]

## Research gap

Existing VideoQA datasets lack fine-grained temporal and spatial annotations — models can answer questions by pattern-matching without genuine temporal localisation or entity-level spatial reasoning. This paper addresses both gaps with two contributions: (1) **MOMA-QA**, a new benchmark derived from the MOMA (Multi-Object Multi-Actor) dataset featuring 300K+ QA pairs with ground-truth temporal intervals and bounding box augmentations, and (2) **SGVLM** (Scene Graph Video Language Model), a video-language model that integrates a frame encoder, a scene graph predictor, and an efficient frame localizer for fine-grained spatiotemporal video QA.

## Contributions

- **MOMA-QA dataset**: 300,791 QA pairs across 3 question types (relationship, motion, description) with ground-truth temporal interval annotations and bounding box augmentations — specifically designed to require temporal localisation and spatial entity reasoning
- **SGVLM architecture**: integrates a scene graph predictor into the video-language pipeline, enabling explicit spatial relationship understanding alongside temporal localisation
- **Frame localizer with self-attention masking**: restricts frame and scene graph tokens to attend only to question tokens, improving question-relevance of selected frames and boosting moment retrieval performance
- **Bounding box augmentations**: a technique that zooms into entity-centric regions of video frames to reduce ambiguity in multi-actor scenes, improving entity-specific reasoning

## Method


## Datasets & evaluation

**Zero-shot MOMA-QA:**

| Model | Accuracy | WUPS@0.9 |
|-------|----------|----------|
| InternVideo | 50.00 | 0.1036 |
| mPLUG-2 | 53.19 | 0.2081 |
| BLIP-2 | 50.00 | 0.0000 |
| SeViLa | 51.22 | 0.2277 |
| **SGVLM** | **58.69** | **0.3283** |

**Fine-tuned MOMA-QA:**

| Model | Accuracy | WUPS@0.9 |
|-------|----------|----------|
| SeViLa | 63.42 | 0.6064 |
| **SGVLM** | **66.64** | **0.6643** |

**NExT-QA (fine-tuned):**

| Type | SGVLM | SeViLa |
|------|-------|--------|
| Causal | 75.2 | 67.01 |
| Temporal | 66.3 | 65.51 |
| Descriptive | 83.4 | 79.25 |
| **Average** | **74.3** | **74.3** |

**Moment Retrieval & Highlight Detection (MOMA-QA):** R1@0.5 44.65, mAP 46.06 — ranks second overall, leads in HD IoU>Very Good threshold. Attention mask ablation confirms +0.97% advantage on mAP@0.5.

## Limitations

- MOMA-QA is derived from a controlled activity dataset; generalisability to in-the-wild videos with diverse scenes and actors is untested
- Scene graph predictor requires pre-training on Visual Genome and fine-tuning on annotated data — not zero-shot generalisable to new object categories
- Bounding box augmentations require object detection annotations at inference; this dependency limits deployment in unannotated settings
- NExT-QA improvement over SeViLa is marginal overall (74.3% vs 74.3%) — scene graphs primarily help relationship and description questions, less so temporal reasoning

## Key takeaways

SGVLM achieves SOTA on MOMA-QA in both zero-shot and fine-tuned settings, and competitive performance on NExT-QA, while also performing strongly on moment retrieval and highlight detection tasks.

- **SeViLa** (Yu et al., 2023): prior SOTA on VideoQA with frame localiser; SGVLM surpasses it by 3.04% on MOMA-QA fine-tuned accuracy and substantially on zero-shot metrics by integrating scene graphs
- **TimeSearch-R** ([[papers/2025-timesearch-r-temporal-search-video-understanding|Pan et al., 2025]]): both papers address temporal frame selection for VideoQA, but from different angles — TimeSearch-R uses RL to learn adaptive search; SGVLM uses a supervised localizer with scene graph context. Complementary approaches.
- **VTG-LLM / Q-Former**: SGVLM's frame localizer and scene graph encoder are based on these components; SGVLM's contribution is their integration with explicit scene graph prediction
